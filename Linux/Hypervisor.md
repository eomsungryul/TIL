# 리눅스의 하이퍼바이저(Hypervisor)란?

하이퍼바이저는 물리적 하드웨어 위에서 가상 머신(Virtual Machine, VM)을 실행할 수 있도록 지원하는 소프트웨어입니다. 리눅스에서 하이퍼바이저는 주로 **KVM**(Kernel-based Virtual Machine)과 같은 솔루션을 통해 구현되며, 하드웨어의 효율적인 사용과 가상화된 환경을 제공합니다.

---

## 리눅스 하이퍼바이저의 주요 특징

### 1. **KVM (Kernel-based Virtual Machine)**
- 리눅스 커널에 통합된 하이퍼바이저 기능.
- 가상화를 지원하는 CPU(Intel VT-x, AMD-V)에서 동작.
- **qemu**와 함께 사용하여 완전한 가상화를 지원.
- 하드웨어와 직접 상호작용하며 고성능 제공.

### 2. **Xen 하이퍼바이저**
- Xen Project에서 개발된 독립형 하이퍼바이저.
- 도메인0(Dom0)이라는 특수한 VM에서 하드웨어 제어를 담당.
- DomU라는 게스트 VM에서 운영 체제를 실행.

### 3. **컨테이너 기반 가상화 (비교를 위해 언급)**
- Docker와 같은 기술은 전통적인 하이퍼바이저와 달리 OS 레벨에서 가상화.
- 리눅스 커널의 **cgroups**와 **namespace**를 활용.
- 하이퍼바이저와는 다른 방식으로 경량화된 가상화를 제공.

---

## 리눅스 하이퍼바이저의 장점

1. **효율적인 자원 활용**  
   가상 머신이 독립적으로 동작하면서도 물리적 자원을 공유하여 비용 절감 가능.

2. **유연한 확장성**  
   하나의 물리적 서버에서 다수의 가상 서버를 실행 가능.

3. **보안 격리**  
   각 VM이 독립된 환경에서 실행되어 보안성이 높음.

4. **하드웨어 추상화**  
   운영 체제와 하드웨어 간의 결합을 줄여 이식성과 관리 용이성을 제공.

---

## 리눅스 하이퍼바이저의 구조

```plaintext
+---------------------+
|     Guest OS        | <-- 여러 개의 VM (Virtual Machines)
+---------------------+
|     Hypervisor      | <-- KVM, Xen 등
+---------------------+
|     Host OS         | <-- 리눅스 커널
+---------------------+
| Physical Hardware   | <-- CPU, RAM, Storage 등
+---------------------+