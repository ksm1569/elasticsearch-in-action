# 1. 엘라스틱서치 이미지 땡겨오기
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.12.2
</br></br>

# 2. 네트워크 설치 - 컨터이너끼리 network로 묶을 수 있음
docker network create es-network
</br></br>

# 3. 엘라스틱 서치 포트 및 네트워크 설정하여 실행
docker run --name es-01 --net es-network -p 9200:9200 -it -m 1GB docker.elastic.co/elasticsearch/elasticsearch:8.12.2
</br></br>

# 4. 도커에있는 http_ca 인증서 현재경로에 받아오기
docker cp es-01:/usr/share/elasticsearch/config/certs/http_ca.crt .
</br></br>

# 5. 받아온 http_ca 인증서로 통신체크 
(QswEm3qGZENN0j7iNfPL 은 비밀번호)
curl --cacert http_ca.crt -u elastic:QswEm3qGZENN0j7iNfPL -XGET https://localhost:9200
</br></br>

# 6. 노드정보 확인 해보기
curl --cacert http_ca.crt -u elastic:QswEm3qGZENN0j7iNfPL -XGET 'https://localhost:9200/_cat/nodes?v'
</br></br>

### 노드정보 예시
### ip         heap.percent ram.percent cpu load_1m load_5m load_15m node.role   master name
### 172.18.0.2           58          94   2    2.49    2.98     2.83 cdfhilmrstw *      959eac75d942
</br>

# 7. 노드 추가 실행 해보기
### 기존 노드에 enrollment token 발급하여 연결해주기
docker exec -it es-01 /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s node

### 결과물로 나오는 토큰으로 30분동안 노드연결이 가능해진다.
### eyJ2ZXIiOiI4LjEyLjIiLCJhZHIiOlsiMTcyLjE4LjAuMjo5MjAwIl0sImZnciI6ImQ0MDQ5OGZhMTM0MjI5NDRlMDg5ODg5MjI0Njk3YzU5NzdkZmM0Mjg3NGM1ZTg3N2UzYWRlZTg1MjkxMmFmMmIiLCJrZXkiOiJTck8xcVpJQlZkMHFKS0VrUGQ2bTpKQ1h2S1pnY1JrLWk2VGEwSm83bUFnIn0=
</br></br>

# 8. 노드 추가해주기
docker run -e ENROLLMENT_TOKEN="eyJ2ZXIiOiI4LjEyLjIiLCJhZHIiOlsiMTcyLjE4LjAuMjo5MjAwIl0sImZnciI6ImQ0MDQ5OGZhMTM0MjI5NDRlMDg5ODg5MjI0Njk3YzU5NzdkZmM0Mjg3NGM1ZTg3N2UzYWRlZTg1MjkxMmFmMmIiLCJrZXkiOiJTck8xcVpJQlZkMHFKS0VrUGQ2bTpKQ1h2S1pnY1JrLWk2VGEwSm83bUFnIn0=" --name es-02 --net es-network -it -m 1GB docker.elastic.co/elasticsearch/elasticsearch:8.12.2
</br></br>


# 9. 해당 클러스터에 추가한 노드 잘 붙었나 체크
curl --cacert http_ca.crt -u elastic:QswEm3qGZENN0j7iNfPL -XGET 'https://localhost:9200/_cat/nodes?v'


### ip         heap.percent ram.percent cpu load_1m load_5m load_15m node.role   master name
### 172.18.0.3           25          93   3    3.20    3.17     3.22 cdfhilmrstw -      dabd4cd04006
### 172.18.0.2           46          78   3    3.20    3.17     3.22 cdfhilmrstw *      959eac75d942
</br></br>

# 10. 공식홈페이지로 이동하여 아래 두개 파일 다운로드 
https://www.elastic.co/guide/en/elasticsearch/reference/8.12/docker.html#_next_steps_5
<br>
10-1. .env파일 <br>
10-2. docker-compose.yml파일 <br>
10-3. env파일에 설정 정보 수정 후 아래 명령 실행하면 끝 <br>
10-4. docker-compose up -d <br>
<br><br>

# 11. 키바나로 이동해서 dev tools로 체크
### 아래 url들어가서 설정해준 아이디(elastic)/비밀번호 입력 후 로그인
localhost:5601
<br><br>

# 12. dev tools에서 노드정보 및 클러스터 헬스체크 해보기
GET _cat/nodes?v
GET _cluster/health
<br><br>
