# Overview

효율적으로 애플리케이션을 관리하기 위해 자주 사용되는 네임 스페이스, 컨피그맵, 시크릿의 사용법을 알아보겠습니다.

# Namespace

네임 스페이스는 용도에 따라 컨테이너와 그에 관련된 리소스들을 구분 지어 관리할 수 있는 일종의 논리적 그룹이 있으면 좋을 것 입니다. 쿠버네티스에서는 리소스를 논리적으로 구분하기 위해 네임 스페이스라는 오브젝트를 제공합니다. 

## Namespace 개념

네임 스페이스는 namespace 또는 ns라는 이름으로 쿠버네티스에서 사용할 수 있습니다. 
```
kubectl get namespaces

NAME
default
kube-public
kube-system
```
어떤 네임스페이스를 생성하지 않았더라도 기본적으로 3개의 네임 스페이스는 존재합니다. 각 네임 스페이스는 논리적인 리로스 공간이기 때문에 각 네임 스페이스에는 포드, 레플리카셋, 서비스와 같은 리소스가 따로 존재합니다.  만약 default 네임스페이스의 생성된 포드를 확인하려면 아래와 같은 명렁어를 수행하면 됩니다. 

```
kubectl get pods --namespace default -- default 네임스페이스 구성보기
```

default는 쿠버네티스를 설치하면 자동으로 설정되는 네임스페이스입니다. kubectl 명령어로 쿠버네티스 리소스를 사용할때 기본적으로 default 네임스페이스를 사용합니다. 

```
kubectl get pods -n kube-system
...
```

위의 명령어를 수행해보면 생성한 적 없는 포드가 여러개 실행되고 있습니다. kube-system 네임스페이스는 쿠버네티스 클러스터 구성에 필수적인 컴포넌트들과 설정값 등이 존재하는 네임스페이스 입니다. 지금껏 써왔던 default 네임스페이스와 논리적으로 구분되어 있기 때문에 위의 명렁어를 수행했을때 나오는 많은 포드들을 본적이 없는것 입니다. 

>kube-system 네임스페이스는 쿠버네티스에 대한 충분한 이해 없이는 건드리지 맙시다. 예상치 못하게 쿠버네티스 클러스터가 동작하지 않을 수도 있기 때문입니다. 

물론 서비스, 레플리카셋을 비롯한 여러 리소스들도 각 네임스페이스에 별도로 존재합니다. 네임스페이스는 쿠버네티스의 리소스를 논리적으로 묶을 수 있는 가상 클러스터처럼 사용할 수 있습니다. 쿠버네티스 클러스터를 여러명이 동시에 사용해야 한다면 사용자마다 네임스페이스를 별도로 생성해 사용하도록 설정할 수도 있습니다. 

앞으로 여러분이 네임스페이스를 사용하는 경우는 대부분 모니터링, 로드 밸런싱 인그레스등 특정 목적을 위한 용도가 대부분일것 입니다. 그렇지만 각 네임스페이스는 논리적으로 구분된것일 뿐 물리적으로 격리되어 있진 않습니다. 예를 들어, 서로 다른 네임 스페이스에서 생성된 포드가 같은 노드에 존재할 수 있습니다. 

## Namespace 사용하기

```yaml
apiVersion: v1
kind: Namespace
metadata: 
  name: production
```

위 yml을 실행하면 네임스페이스 목록에 production이라는 네임스페이스가 새로 생성되어 있습니다. 해당 production 네임 스페이스에 리소스를 생성하는 방법은 YAML파일에서 metadata.namespace 항목을 설정하면 됩니다. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: hostname-deployment-ns\#
  namespace: production
spec: 
	... 
```

참고로 kubectl get 명렁어로 --all --namespaces 옵션을 사용하면 모든 네임스페이스의 리소스를 확인 할수도 있습니다. 

## 네임스페이스 서비스 접근하기

쿠버네티스 클러스터 내부에서는 서비스 이름을 통해 포드에 접근할 수 있습니다. 이는 정확히 말하자면 **같은 네임스페이스 내의 서비스에 접근할때에는 서비스 이름만으로 접근할수 있다는 뜻입니다.** 

다른 네임스페이스에 존재하는 서비스에는 서비스 이름만으로 접근할 수는 없습니다.  하지만 <서비스이름>.<네임스페이스 이름>.svc 처럼 서비스 이름 뒤에 네임스페이스 이름을 붙이면 다른 네임스페이스의 서비스에서도 접근할 수 있습니다. 

```
curl host-name-svc-clusterip-ns.production.svc:8080 
```

네임스페이스를 삭제하게 되면 네임스페이스 안의 존재하는 모든 리소스 또한 함께 삭제되기 때문에 네임스페이스를 삭제하기전에 리소스 목록을 확인해봅시다. 

## 네임스페이스에 종속되는 쿠버네티스 오브젝트와 독립적인 오브젝트

네임스페이스를 사용하면 리소스를 사용 목적에 따라 논리적으로 격리할 수 있지만, 모든 리소스가 네임스페이스에 의해 구분되는 것은 아닙니다.  

특정 네임스페이스에 속하는 오브젝트는 다른 네임스페이스에서는 보이지 않습니다. 이런 경우를 `오브젝트가 네임스페이스에 속한다(namespaced)`라고 표현합니다. 

```
kubectl api-resources --namespaced=true  # 네임스페이스에 속한 오브젝트 보기
```

반대로 특정 네임스페이스에 속하지 않는 독립적인 오브젝트도 있습니다. 

```
kubectl api-resources --namespaced=false  # 네임스페이스에 속하지 않은 오브젝트 보기
```

노드는 물론 네임스페이스 자체도 네임스페이스에 속하지 않는 것을 확인할 수 있습니다. 




# Configmap & Secret

애플리케이션은 대부분 설정 값을 가지고 있습니다.  예를 들어 애플리케이션 로깅 레벨, nginx.conf 처럼 하나의 파일이 있을 수도 있습니다. 

이러한 설정값이나 설정 파일을 애플리케이션에 주는 가장 확실한 방법은 도커 이미지 내부에 불변 상태로 저장해 두는 것입니다. 하지만 도커 이미지는 일단 빌드되고 나면 불변이기 때문에 이 방법은 상황에 따라 설정 옵션을 유연하게 변경할 수 없다는 단점이 있습니다. 

대안으로 포드를 정의하는 YAML 파일에 환경변수를 직접 적어 놓는 하드 코딩 방식을 사용할 수도 있습니다. 
```
  spec:
    containers:
    - name: nginx
      env:
      - name: LOG_LEVEL
        value: INFO
      image: nginx: 1.10
```

위 처럼 LOG_LEVEL이라는 이름의 환경 변수를 INFO라고 정의할 수도 있습니다.  포드 템플릿에 직접 명시하는 방법도 나쁘지는 않지만, 상황에 따라 환경 변수의 값만 다른 동일한 여러 YAML이 존재할 수도 있습니다. 

쿠버네티스는 YAML 파일과 설정값을 분리할 수 있는 컨피그맵과 시크릿이라는 오브젝트를 제공합니다.  컨피그 맵에는 설정값을 시크릿에는 노출되어서는 안되는 비밀값을 저장할 수 있습니다. 

먼저 컨피그 맵을 사용하는 법을 보다 자세히 알아보겠습니다. 

## Configmap 

컨피그맵은 **일반적인 설정값을 담아 저장할 수 있는 코버네티스 오브젝트**이며, **네임스페이스에 속하기 때문에 네임스페이스별로 컨피그맵**이 존재합니다. 

컨피그맵은 YAML을 이용해서 생성해도 되지만 명령어를 이용해서도 가능합니다.

```yaml
kubectl create configmap <커피그맵 이름><각종 설정값들>
kubectl create configmap log-level-configmap --from-literal LOG_LEVEL=DEBUG
# log-level-configmap 컨피그 맵에 LOG_LEVEL=DEBUG 키값 저장하기
kubectl create configmap start-k8s --from-literal k8s=kubernates \ --from-literal container=docker
# start-k8s 컨피그 맵에 k8s=kubernates & container=docker 두개의 키-값 저장하기
```

아래는 YAML로 작성된 컨피그맵입니다.
```
apiVersion: v1
data:
  LOG_LEBEL: DEBUG
kind: ConfigMap 
```

컨피그 맵을 포드에서 가져와서 사용하는 방법은 크게 두 가지 방법이 있습니다. 하나씩 알아봅시다.

### 컨피그맵의 값을 컨테이너의 환경변수로 가져오기 

먼저 컨피그맵의 데이터를 환경변수로 사용하는 포드를 만들어 보겠습니다. 컨피그 맵에 저장된 키-값 데이터가 컨테이너의 환경 변수의 키-값으로서 그대로 사용되기 때문에 셀에서도 값을 확인할 수 있습니다. 애플리케이션이 시스템 환경변수로부터 설정값을 가져온다면 이 방법이 좋습니다. 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name : container-env-example
spec:
  containers:
    - name: my-container
      image: busybox
      args: ['tail', '-f', '/dev/null']
      envFrom:
      - configMapRef:
	      name: log-level-configmap # 키-값쌍이 하나만 존재하는 컨피그맵
      - configMapRef:
	      name: start-k8s # 키-값쌍이 두개 존재하는 컨피그맵
```

위의 YAML 파일 내용 중 중요한 부분은 envFrom과 ConfigMapRef 부분입니다. 두개의 컨피그맵으로 부터 값을 가져와 환경변수를 생성하도록 설정했습니다. 

YAML 파일에서 envFrom 항목은 여러개의 키-값 쌍이 존재하더라도 모두 환경 변수로 가져옵니다. 즉 위에선 3개의 키-값 쌍을 포드로 넘긴 셈입니다. 

포드를 생성한 뒤 포드 내부에서 환경변수 목록을 호출해보고 싶다면 env 명령어를 사용하면 됩니다.



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU1Mjk0NDcwNF19
-->