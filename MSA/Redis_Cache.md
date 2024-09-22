<!--
자바 msa 프로젝트이고, ui에서 java서버에 ajax로 통신할거야. DB는 clob으로 되어 있어. 
디비에 저장하는 동한 redis 캐시를 사용하여 파일을 저장하고, 다운로드 할때,  
redis에서 파일을 읽어 올거야. redis에 파일이 없는 경우. DB에서 파일을 읽어들이고, 
redis에 파일을 저장하고, 다운로드 결과를 내보낼거야. 이에 대한 대용량 파일 업로드 다운로드 소스 보여줘. 
업로드 다운로드는 공통 유틸로 만들거야. redis는 cachable을 사용해서 읽을거야
-->


#  MSA 구조를 기반으로 파일 업로드와 다운로드를 처리
Spring Boot와 Redis 캐시, CLOB 타입 DB를 사용하는 MSA 구조를 기반으로 파일 업로드와 다운로드를 처리하는 방법을 구현합니다.

### 1 Dependencies
Redis 캐시와 관련된 Spring Boot 의존성을 pom.xml에 추가합니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
````

### 2. Redis 설정
Redis 캐시와 관련된 Spring Boot 의존성을 pom.xml에 추가합니다.

```yaml
spring:
  redis:
    host: localhost
    port: 6379
    cache:
      type: redis

```



### 3. 공통 유틸 클래스
대용량 파일 업로드 및 다운로드를 위한 공통 유틸 클래스를 만듭니다.
```java
import org.springframework.stereotype.Component;

@Component
public class FileUtil {

    // 파일 처리 (CLOB 변환 등)
    public void handleFile(String fileId, byte[] fileData) {
        // 파일 관련 처리 로직
    }

    // 예: 파일 이름 생성, 파일 검증 등 추가 가능
}

```

### 4. FileEntity (DB 저장용 엔티티)
DB에 파일을 CLOB 타입으로 저장하는 엔티티 클래스입니다.
```java
import lombok.Builder;
import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
@Data // Getter, Setter, toString 등 자동 생성
@Builder // 빌더 패턴 지원
public class FileEntity {

    @Id
    private String fileId;
    private String fileData; // CLOB 타입으로 저장
}

```

### 5. FileService
DB와 Redis에 파일을 저장하고 조회하는 서비스 클래스.
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.annotation.CachePut;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.util.Optional;

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
        return saveFileToCache(fileId, fileData);
    }

    // Redis에 파일 저장 (file_cache:)
    @CachePut(value = "fileCache", key = "#fileId")
    public byte[] saveFileToCache(String fileId, byte[] fileData) {
        // return을 CachePut함으로 특별한 경우 아니면 필요하지 않다.
        // redisTemplate.opsForValue().set("file_cache:" + fileId, fileData); 
        return fileData; // 캐시된 파일 데이터를 반환
    }

    // 파일 다운로드 (캐시 조회 및 DB 조회)
    @Cacheable(value = "fileCache", key = "#fileId")
    public byte[] downloadFile(String fileId) {
        // DB에서 파일을 가져옴
        FileEntity fileEntity = fileRepository.findByFileId(fileId);
        return Optional.ofNullable(fileEntity)
                .map(FileEntity::getFileData)
                .map(String::getBytes)
                .orElse(null); // DB에서 조회한 파일 데이터 반환
    }
}

```

### 6. FileRepository (JPA 리포지토리)
JPA 리포지토리 인터페이스를 생성합니다.
```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface FileRepository extends JpaRepository<FileEntity, String> {
    // 기본적인 CRUD 메서드는 JpaRepository에서 제공됨
    FileEntity findByFileId(String fileId);
}

```


### 7. 파일 업로드/다운로드 컨트롤러
파일 업로드 및 다운로드를 처리하는 REST 컨트롤러.
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;

@RestController
@RequestMapping("/files")
public class FileController {

    @Autowired
    private FileService fileService;

    // 파일 업로드
    @PostMapping("/upload/{fileId}")
    public ResponseEntity<String> uploadFile(@PathVariable String fileId, @RequestParam MultipartFile file) {
        try {
            byte[] fileData = fileService.uploadFile(fileId, file);
            return ResponseEntity.ok("File uploaded successfully with ID: " + fileId);
        } catch (IOException e) {
            return ResponseEntity.status(500).body("Error uploading file: " + e.getMessage());
        }
    }

    // 파일 다운로드
    @GetMapping("/download/{fileId}")
    public ResponseEntity<byte[]> downloadFile(@PathVariable String fileId) {
        byte[] fileData = fileService.downloadFile(fileId);
        if (fileData != null) {
            return ResponseEntity.ok(fileData);
        } else {
            return ResponseEntity.notFound().build();
        }
    }


    // 파일 업로드
    @PostMapping("/upload")
    public ResponseEntity<String> uploadFile(@RequestParam("fileId") String fileId, @RequestParam("file") MultipartFile file) {
        try {
            fileUtil.uploadFile(fileId, file, fileRepository);
            return new ResponseEntity<>("File uploaded successfully", HttpStatus.OK);
        } catch (IOException e) {
            return new ResponseEntity<>("File upload failed", HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    // 파일 다운로드
    @GetMapping("/download")
    public ResponseEntity<byte[]> downloadFile(@RequestParam("fileId") String fileId) {
        byte[] fileData = fileUtil.downloadFile(fileId, fileRepository);
        if (fileData != null) {
            HttpHeaders headers = new HttpHeaders();
            headers.add(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=" + fileId);
            return new ResponseEntity<>(fileData, headers, HttpStatus.OK);
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }
}

```


### 7. Redis 캐싱 설정
@Cacheable을 사용할 수 있도록 Spring에서 캐시 설정을 추가해야 합니다.

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

### 8. Test 
- [file-upload-download.html](file-upload-download.html)
  - Ajax를 사용하여 파일을 업로드하고 다운로드하는 간단한 HTML과 JavaScript UI 예제


### 9. 문제 및 고찰
1. @EnableCaching 문제
   - 애너테이션이 Spring의 캐시 기능을 활성화하는 역할을 합니다.
   - Spring의 캐시 추상화가 Redis에 자동으로 저장하게 됩니다. 
   - 즉, Redis와 Spring의 캐시 모두에 저장되므로, 두 시스템이 연동되어 데이터를 효율적으로 관리할 수 있습니다.
     - 데이터가 매우 큰 경우 Spring 캐시에 저장하는 것은 서버 메모리 문제를 일으킬 수 있습니다.

2. @EnableCaching 에 대한 대안
   - [스프링 캐시와 Redis 혼용 사용](Redis_SpringCashe.md)
     - 필요한 경우에만 Spring 캐시를 사용하고, 데이터 크기가 큰 경우 Redis만 사용하는 방법을 있습니다. 

