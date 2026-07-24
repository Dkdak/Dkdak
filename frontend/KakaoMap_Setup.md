# Kakao Map API 설정 가이드

리모델링 투자 엔진(Rebuild)에서 카카오맵을 사용하기 위한 설정 방법입니다.

---

# 1. 카카오 디벨로퍼스 접속

https://developers.kakao.com

카카오 계정으로 로그인합니다.

---

# 2. 애플리케이션 생성

메뉴

```
앱
```

새 애플리케이션 생성

|항목| 입력값                 |
|----|---------------------|
|앱 이름| Rebuild             |
|회사명| <<개인이름 또는 회사명>>     |
|카테고리| 부동산/인테리어            |
|대표 도메인| 비워둠(도메인 생성 후 수정 가능) |

생성 후 대시보드로 이동합니다.

---

# 3. 카카오맵 활성화

- 메뉴 > 제품 설정 > 카카오맵
- 상태를 ON 으로 변경합니다.

---

# 4. JavaScript 키 생성

- 메뉴 > 앱 > 플랫폼 키
- 기존 JavaScript 키의 대표을 클릭합니다.
- 또는 JavaScript 키 추가 를 선택합니다.
- 다음과 같이 입력합니다.
  - 키 이름 : Rebuild Local 
  - JavaScript SDK 도메인 (개발용 주소 등록)
    ```
    http://localhost:5173
    <<서버 주소>>
    ```
  - 카카오 로그인 Redirect URI: 현재는 사용하지 않으므로 비워둡니다.
    ```
    (비워둠)
    ```
- 저장을 누르면 JavaScript 키가 생성됩니다.

---

# 5. JavaScript SDK 사용

React(Vite)의 index.html 또는 동적 로딩에서 사용합니다.

```html
<script
src="https://dapi.kakao.com/v2/maps/sdk.js?appkey=발급받은_JavaScript_키&autoload=false">
</script>
```

---

# 6. React 컴포넌트 사용 예시

`index.html`에 스크립트를 넣는 대신, 컴포넌트 마운트 시점에 SDK를 동적으로 로드하는 방식입니다. `autoload=false`로 받아서 `kakao.maps.load` 콜백 안에서 지도를 그리면, SDK 로드 완료 시점을 안전하게 보장할 수 있습니다.

```tsx
// KakaoMap.tsx
import { useEffect, useRef } from 'react';

declare global {
  interface Window {
    kakao: any;
  }
}

const KAKAO_APP_KEY = '발급받은_JavaScript_키';

interface KakaoMapProps {
  lat: number;
  lng: number;
  level?: number; // 확대 레벨 (숫자가 작을수록 확대)
}

export default function KakaoMap({ lat, lng, level = 3 }: KakaoMapProps) {
  const mapRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const script = document.createElement('script');
    script.src = `https://dapi.kakao.com/v2/maps/sdk.js?appkey=${KAKAO_APP_KEY}&autoload=false`;
    script.async = true;
    document.head.appendChild(script);

    script.onload = () => {
      window.kakao.maps.load(() => {
        if (!mapRef.current) return;

        const map = new window.kakao.maps.Map(mapRef.current, {
          center: new window.kakao.maps.LatLng(lat, lng),
          level,
        });

        new window.kakao.maps.Marker({
          position: new window.kakao.maps.LatLng(lat, lng),
          map,
        });
      });
    };

    return () => {
      document.head.removeChild(script);
    };
  }, [lat, lng, level]);

  return <div ref={mapRef} style={{ width: '100%', height: '400px' }} />;
}
```

사용하는 쪽에서는 좌표만 넘겨주면 됩니다.

```tsx
<KakaoMap lat={37.5665} lng={126.9780} />
```

---

# 현재 등록된 개발 환경

로컬

```
http://localhost:5173
```

운영/개발 서버

```
<<서버 주소>>
```

추후 운영 시

```
https://도메인
```

을 추가 등록하면 됩니다.

---

# 참고

카카오 로그인 기능을 사용할 경우에는

```
카카오 로그인 Redirect URI
```

를 추가 등록해야 합니다.

예)

```
http://localhost:5173/login/oauth2/code/kakao
```

---

# 다음 단계

- React에서 카카오맵 출력
- Spring Boot와 연동
- PostgreSQL 건물 데이터 조회
- 건물 마커 표시
- 건물 클릭 시 상세정보 조회
