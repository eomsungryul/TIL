# 📌 CKA 스터디 - 1주차

## 🎯 교육명
**Prepare for the Certified Kubernetes Administrators Certification with live practice tests right in your browser - CKA**

🔹 **할인 코드**: `20KODE` (20% 할인 가능)  
🔹 **수강 링크**: [CKA 공식 사이트](https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/?cjdata=MXxOfDB8WXww&cjevent=26b1fe66ea7111ef80bb00230a18ba74&utm_source=CJ&utm_medium=affiliate)  
🔹 **비용**: 약 `$445`  
🔹 **GitHub 코스 저장소**: [바로가기](https://github.com/kodekloudhub/certified-kubernetes-administrator-course)

---

## 🗃️ Etcd
- **Key-Value Store 형식**의 데이터 저장소
- 실행 시 **`2379` 포트**에서 동작

---

## 🖥️ Kubernetes 주요 컴포넌트

### 🏛️ 마스터 노드 & 워커 노드 역할
✅ **마스터 노드**
- `etcd` → 쿠버네티스 상태 저장소
- `kube-controller-manager` → 컨트롤러 관리
- `kube-scheduler` → 파드 배치

✅ **워커 노드**
- `kubelet` → 노드에 수동 설치, 노드 등록
- `kube-proxy` → Pod 간 네트워크 통신

🔹 **CRI(Container Runtime Interface)**
- 다양한 컨테이너 런타임을 쿠버네티스에서 사용할 수 있도록 도와주는 인터페이스

---

## 📜 YAML 기본 구조
쿠버네티스 리소스 정의 시 4가지 주요 필드

```yaml  
apiVersion: # API 버전  
kind:       # 리소스 종류 (Deployment, Pod 등)  
metadata:   # 이름, 라벨 등 메타데이터  
spec:       # 상세 스펙  
```

---

## 🛠️ 실습 1 - Redis Pod 생성

```sh  
kubectl run redis --image=redis123 --dry-run=client -o yaml > redis.yaml  
kubectl apply -f redis.yaml  
```

✅ `--dry-run` 옵션을 사용하면 리소스 파일을 자동 생성 가능

---

## 🔁 ReplicaSet & ReplicationController
- `v1`에서는 **ReplicationController**를 사용하지만, **ReplicaSet을 권장**
- 리소스 단축어 지원:  
  ```sh  
  kubectl get po, no, deploy, rs, ds  
  ```
- `apply -f` vs `replace -f` 차이점 실습 필요
- `kubectl get all -A` → 모든 리소스 조회

---

## 🚀 Deployment
✅ **실무에서는 거의 항상 Deployment 사용** (ReplicaSet 직접 사용 X)

- 1️⃣ Deployment를 생성하면 **내부적으로 ReplicaSet이 자동 생성**됨.  
- 2️⃣ ReplicaSet은 **정해진 개수의 Pod를 실행 및 유지**.  
- 3️⃣ 새로운 버전(image 변경 등)으로 업데이트하면:
  - 새로운 **ReplicaSet이 생성**됨.
  - **Rolling Update 진행** (점진적 트래픽 이동).
  - 기존 **ReplicaSet의 Pod가 점진적으로 삭제**됨.  
- 4️⃣ 문제가 발생하면 **Deployment 롤백 가능**.

---

## 🔗 Service
✅ Pod 간 통신을 위한 네트워크 서비스 제공  
✅ 기본 포트 범위: `30,000` ~ `32,767`

---

## 🏛️ 명령형 vs 선언형

✅ **선언형이 실무에서는 더 선호됨** 😆
- YAML을 선언하고 `kubectl apply` 명령어로 적용하면 자동으로 관리됨.
- 유지보수와 재현성이 뛰어남.

✅ **시험에서는 명령형 사용 빈도가 높음**
- 빠르게 특정 리소스를 생성/수정할 때 사용.

---

## 🚀 스케줄러

- **이미 Node에 있는 Pod는 이동할 수 없음** → 이동하려면 **삭제 후 재배포**해야 함.
- 특정 노드에 배치하려면 `nodeName` 사용.

---

## ⚠️ Taint & Toleration

- **Toleration** → 특정 Taint가 설정된 노드에서도 실행 가능하게 함.
- Taint가 있는 노드에 Pod이 배치되는 것을 보장하지 않음.
- **마스터 노드는 기본적으로 Taint가 설정됨**.

### ✅ **Taint 설정 명령어**
```sh  
kubectl taint nodes {nodeName} {key}={value}:{effect}  
```  
➡️ `-`를 붙이면 해당 taint 제거 가능!

---

## 🔗 Node Affinity

✅ **기본적인 Node 배치 정책**

| 상태  | 필수 여부 | 실행 상태 |
|-------|----------|----------|
| `requiredDuringSchedulingIgnoredDuringExecution` | 필수(required) | 실행 중 변경 사항 무시 |
| `preferredDuringSchedulingIgnoredDuringExecution` | 선호(preferred) | 실행 중 변경 사항 무시 |
| `requiredDuringSchedulingRequiredDuringExecution` | 필수(required) | 실행 중에도 강제 적용 (계획됨) |
| `preferredDuringSchedulingRequiredDuringExecution` | 선호(preferred) | 실행 중에도 강제 적용 (계획됨) |

✅ **Affinity 적용 시 라벨이 변경되더라도 기존 배치는 유지됨.**

![Node Affinity](CKA_img/Node_affinity.png)

---
## 🔹 Scheduling
### ✅ **request nolimits 가 제일 이상적**
- Kubernetes에서는 `requests`와 `limits`를 설정하여 Pod의 리소스 사용을 제어할 수 있음.
- 하지만 **이상적인 설정은 request는 설정하되, limits는 설정하지 않는 것** (`request nolimits`).
- 이유:
  - **requests는 최소한의 리소스를 보장**하여 스케줄링이 가능하도록 함.
  - **limits를 설정하면 CPU Throttling(제한)이 걸릴 수 있어 성능 저하 발생 가능**.
  - 실제 운영 환경에서는 **CPU limits를 설정하지 않는 것이 일반적인 모범 사례**.
  - 하지만 메모리(`memory limits`)는 OOM(Out of Memory) 방지를 위해 설정하는 경우가 많음.

## 🔹 Static Pods
### ✅ **Static Pods란?**
- **Kubelet이 직접 관리하는 Pod으로, API Server를 거치지 않고 실행됨.**
- 일반적인 Pod과 다르게 **Kubernetes 스케줄러의 영향을 받지 않음**.
- **마스터 노드의 시스템 데몬이나 중요한 Pod을 실행할 때 사용**.

### ✅ **Static Pod의 주요 특징**
- **API Server와 무관하게 Kubelet이 직접 실행**.
- **다른 노드로 이동하지 않음 (Re-Scheduling 불가)**.
- **파일 시스템에 존재하는 YAML을 기반으로 실행됨**.
- **Static Pod이 삭제되면 Kubelet이 다시 생성**.

### ✅ **Static Pod의 설정 경로**
- 옵션 이름: `pod-manifest-path`
- 기본적으로 **Linux에서는 `/etc/kubernetes/manifests/` 경로**에서 Static Pod을 찾음.
- Windows 환경에서는 기본 경로가 다를 수 있음.

## 📌 Kubernetes PVC (Persistent Volume Claim) 정리
✅ **PVC(Persistent Volume Claim)는 Pod이 저장소를 요청하는 방식**  
✅ **PVC는 PersistentVolume(PV)와 연결되어 데이터를 저장**  
✅ **StorageClass를 사용하면 PV를 동적으로 생성할 수 있음**  
✅ **PVC를 활용하면 Pod이 재시작되더라도 데이터를 유지할 수 있음!**

