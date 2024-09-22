<!--
자바 msa 프로젝트이고, ui에서 java서버에 ajax로 통신할거야. DB는 clob으로 되어 있어. 
디비에 저장하는 동한 redis 캐시를 사용하여 파일을 저장하고, 다운로드 할때,  
redis에서 파일을 읽어 올거야. redis에 파일이 없는 경우. DB에서 파일을 읽어들이고, 
redis에 파일을 저장하고, 다운로드 결과를 내보낼거야. 이에 대한 대용량 파일 업로드 다운로드 소스 보여줘. 
업로드 다운로드는 공통 유틸로 만들거야. redis는 cachable을 사용해서 읽을거야
-->


#  @EnableCaching 사용 문제

Redis를 캐시처럼 사용할 때, Spring의 캐시 추상화와 함께 @Cacheable을 사용하는 것은 Redis에 데이터를 자동으로 캐시하게 만듭니다. 

만약 Redis에만 데이터를 저장하고 Spring 캐시는 사용하고 싶지 않다면, @Cacheable을 사용하는 것은 적합하지 않습니다.

작은 용량으로 Redis와 스프링 캐시를 사용하고, 대용량은 Redis만 사용하도록 합니다. 


## 1. Redis 캐싱 설정
  - 작은 용량으로 Redis와 스프링 캐시를 사용하는 경우 CacheConfig를 유지하고 RedisCacheManager와 RedisTemplate을 설정하면 됩니다.
    - @Cacheable를 사용하여 Service 구현합니다. 
  - 대용량으로 Redis만 사용하는 경우 redisTemplate를 사용하도록 합니다.
    - @Cacheable를 사용하여 않고, redisTemplate를 사용하여 Redis에 접근하도록 변경합니다. 

### 1. Redis와 스프링 캐시 설정 (그대로 사용)
```java

import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.RedisSerializationContext;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.time.Duration;

@Configuration
@EnableCaching
public class CacheConfig {

    @Bean
    public RedisCacheManager cacheManager(RedisConnectionFactory redisConnectionFactory) {
        RedisCacheConfiguration configuration = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofMinutes(10)) // 기본 캐시 만료 시간 설정
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer()))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer())); // 값에 대한 JSON 직렬화

        return RedisCacheManager.builder(redisConnectionFactory)
                .cacheDefaults(configuration)
                .build();
    }

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(new GenericJackson2JsonRedisSerializer()); // 값에 대한 JSON 직렬화
        return template;
    }

    // @Cacheable을 사용하는 예제 서비스
    @Service
    public class ExampleService {
        @Cacheable(value = "exampleCache", key = "#id")
        public ExampleEntity getExampleById(Long id) {
            // 데이터베이스 호출을 시뮬레이션
            return findExampleById(id);
        }

        private ExampleEntity findExampleById(Long id) {
            // 데이터베이스에서 엔티티를 검색하는 로직
            return new ExampleEntity(id, "샘플 데이터");
        }
    }
}

```

### 2. Service 구현 
Spring 캐시를 사용하지 않고 Redis만 사용하도록 FileService를 수정합니다.

```java
@Service
public class FileService {

    @Autowired
    private FileUtil fileUtil;

    @Autowired
    private FileRepository fileRepository;

    @Autowired
    private RedisTemplate<String, byte[]> redisTemplate;

    // 파일 업로드 (DB 저장 및 Redis에 캐시)
    public byte[] uploadFile(String fileId, MultipartFile file) throws IOException {
        byte[] fileData = file.getBytes();

        // DB에 파일 저장
        FileEntity fileEntity = FileEntity.builder()
            .fileId(fileId)
            .fileData(new String(fileData)) // CLOB 타입으로 저장
            .build();
        fileRepository.save(fileEntity);

        // Redis에 파일 저장
        saveFileToRedis(fileId, fileData);
        return fileData; // 업로드된 파일 데이터 반환
    }

    // Redis에 파일 저장 (file_cache:)
    public void saveFileToRedis(String fileId, byte[] fileData) {
        redisTemplate.opsForValue().set("file_cache:" + fileId, fileData); // Redis에 파일 저장
    }

    // 파일 다운로드 (Redis 조회 및 DB 조회)
    public byte[] downloadFile(String fileId) {
        // Redis에서 파일 데이터 가져오기
        byte[] fileData = redisTemplate.opsForValue().get("file_cache:" + fileId);

        if (fileData == null) {
            // Redis에 데이터가 없으면 DB에서 가져오기
            FileEntity fileEntity = fileRepository.findByFileId(fileId);
            fileData = Optional.ofNullable(fileEntity)
                    .map(FileEntity::getFileData)
                    .map(String::getBytes)
                    .orElse(null); // DB에서 조회한 파일 데이터 반환
            
            // DB에서 가져온 파일 데이터를 Redis에 저장
            if (fileData != null) {
                saveFileToRedis(fileId, fileData);
            }
        }

        return fileData; // Redis 또는 DB에서 가져온 파일 데이터를 반환
    }
}

```