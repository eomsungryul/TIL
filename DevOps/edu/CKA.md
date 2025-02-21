# CKA 스터디
1주차 교육명

Prepare for the Certified Kubernetes Administrators Certification with live practice tests right in your browser - CKA

- 20KODE 코드로 20% 디스카운트 할 수 있음
- https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/?cjdata=MXxOfDB8WXww&cjevent=26b1fe66ea7111ef80bb00230a18ba74&utm_source=CJ&utm_medium=affiliate
- 445달러?..

- [깃허브 코스 저장소](https://github.com/kodekloudhub/certified-kubernetes-administrator-course)


Etcd는 키 값 형식으로 정보를 저장하는 데이터베이스

마스터 워커노드 역할

CRI라는 인터페이스를 소개했죠
네 CRI는 어떤 공급업체든 쿠버네티스의
컨테이너 런타임으로 작업하게 해줍니다

etcd 
key value store
실행 시 2379 포트에 서 실행 

kube controller 

schedular pod 배치

kubelet 수동 설치해야함 노드 등록 수단 

kube-proxy pod 간 소통
yaml apiVersion kind metadata spec 총 네가지

실습1 
kubectl run redis --image=redis123 --dry-run -o yaml >> redis.yaml
kubectl apply -f redis.yaml
자동으로 만들어줌


replica 관련 
v1 은 replication controller 를 사용하지만 replica set을 권장
kubectl <ex) get> po, no, deploy, rs, ds 등등의 단축어가 있습니다
추후에 실습해보기

apply -f 와 replace -f의 차이
k get all -A 전체보기 

deployment 


service


