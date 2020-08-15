# Kubernetes Object

**쿠버네티스는 상태를 관리해야 하는 대상을 오브젝트로 정의합니다.** 기본으로 수십 가지 오브젝트를 제공하고 새로운 오브젝트를 추가하기가 매우 쉽기 때문에 확장성이 좋습니다. 

컨테이너 애플리케이션을 구동하기 위해 반드시 알아야할 몇 가지 중요한 오브젝트가 있습니다. 바로 Pod, 레플리카셋(Replica Set), 서비스(Service), 디플로이먼트(deployment) 입니다. 하나씩 알아봅시다. 

##  Pod

포드는 컨테이너를 다루는 가장 기본 단위입니다. 포드는 1개 이상의 컨테이너로 구성된 컨테이너의 집합입니다. 즉 1개의 포드에는 다수의 컨태이너가 존재할 수 있습니다. 

**하나의 포드는 개념적으로 하나의 완전한 애플리케이션입니다.** 만약 한 애플리케이션이 실행되기 위해서 부가적인 기능이 필요하다고 한다면, 포드의 메인 컨테이너를 본연의 애플리케이션으로 삼고 부가적인 기능을 사이드카 컨테이너로 삼을 수 있습니다. 이렇게 포드에 정의된 부가적인 컨테이너를 사이드카라고 하며, 사이드카 컨테이너는 포드 내의 다른 컨테이너와 네트워크 환경 등을 공유하게 됩니다. 때문에 포드에 포함된 컨테이너들을 모두 같은 워커 노드에서 함께 동작합니다. 

![쿠버네티스 Pod - 제타위키](https://z-images.s3.amazonaws.com/thumb/7/76/Module_03_pods.svg/700px-Module_03_pods.svg.png)

자 이제 실제 Pod를 만드는 YAML을 작성해봅시다.

```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: my-nginx-pod
spec:
  containers:
    - name: my-nginx-container
      image: nginx:latest
      ports:
      - containerPort: 80
        protocol: TCP     
```

쿠버네티스의 YAML은 일반적으로 apiVersion, kind, metadata, spec 항목으로 구성됩니다. 

apiVersion
: YAML 파일에서 정의한 오브젝트의 버전을 나타냅니다. 오브젝트의 종류 및 개발 성숙도에 따라 apiVersion 값이 달라질 수 있습니다.

kind
: 오브젝트의 종류를 나타냅니다. 사용할 수 있는 오브젝트 종류는 kubectl api-resources 목록의  KIND 항목으로 확인할 수 있습니다.

metadata
: 라벨, 주석, 이름 등과 같은 리소스의 부가 정보를 입력합니다. 위의 예시에서는 포드의 이름으로 my-nginx-pod를 설정했습니다.

spec
: 리로스를 생성하기 위해 보다 자세한 정보를 입력합니다. 위 예시에서는 실행될 컨테이너 정보를 정의하는 containers항목을 작성한뒤  name 하위 항목에서는 컨테이너의 이름, image에서는 도커 이미지, ports 항목에서는 Nginx 컨테이너가 사용할 80 포트를 지정했습니다.

작성된 YAML 파일은 `kubectl apply -f `명령어로 쿠버네티스에서 생성할 수 있습니다. 

## ReplicaSet

![ReplicaSet](https://subicura.com/assets/article_images/2019-05-19-kubernetes-basic-1/replicaset.png)

실제 운영환경에서는 여러 포드를 직접 생성해서 서비스를 하지 않습니다. 동일한 포드의 개수가 많아질 수록 이를 일일이 정의하고 배포하는 것은 매우 비효율적입니다. 특정 포드가 어떠한 이유로든지 삭제되거나, 포드가 위치한 노드에 장애가 발생해 더이상 포드가 접근하지 못하게 되었을때, 직접 포드를 삭제하고 다시 생성하지 않는한 해당 포드는 다시 복구되지 않습니다. 

따라서 쿠버네티스를 사용하면서 포드만 정의해 사용하는 경우는 거의 없습니다. 그리고 이러한 한계를 해결해 주는것은 바로 레플리카 셋입니다. 

레플리카셋은 일정 개수의 포드를 유지하고 실행되도록 관리합니다. 심지어 노드 장애등으로 포드를 사용할 수 없더라도 새로운 포드를 생성하여 실행되도록 합니다. 

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata: 
  name: replicaset-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-nginx-pods-label
  template:
    metadata:
      name: my-nginx-pod
      labels:
        app: my-nginx-pods-label
    spec:
      containers:
    - name: nginx
      image: nginx:latest
      ports:
      - containerPort: 80      
```

spec.replicas
: 동일한 포드를 몇 개 유지시킬지 설정합니다.

spec.template 하위 내용들
: 포드를 생성할때 사용할 템플릿을 설정합니다. 포드를 만들때 사용하던 내용과 동이할게 레플리카 셋에도 적용하여 어떤 포드를 생성할지 명시합니다. 이를 보통 포드 스펙, 포드 템플릿이라고 말합니다.

### ReplicaSet 동작원리

레플리카셋을 보면 마치 레플리카 셋이 다수의 포드와 연결된것 처럼 보입니다. 레플리카셋을 생성하면 포드가 생성되고 레플리카 셋을 삭제하면 포드 또한 모두 삭제되기 때문입니다. 그러나 실제론 **레플리카셋은 포드와 연결되어 있지 않습니다**.  오히려 느슨한 연결을 유지하고 있으며, 이런 느슨한 연결은 포드와 레플리카셋의 정의 중 라벨 셀렉터(Label Selector)를 이용해 이루어집니다.

라벨은 포드 등의 쿠버네티스 리로스를 분류할때 유용하게 사용할 수 있는 메타 데이터 입니다. 라벨은 쿠버네티스 리소스의 부가적인 정보를 표현할 수 있을 뿐만 아니라 서로 다른 오브젝트가 서로를 찾아야할때도 사용됩니다. 

예를 들어, 레플리카 셋은 **spec.selector.matchLabel**에 정의된 라벨을 통해서 생성해야할 포드를 찾습니다. 앞서 예제에서는 `app: my-nginx-pods-label` 라벨을 가지는 포드의 개수가 replicas 항목에 정의된 숫자인 3과 일치하지 않으면 포드를 정의하는 포드 템플릿 내용으로 새 포드를 생성하여 유지합니다. 

레플리카셋을 처음 생성했을때는  `app: my-nginx-pods-label`을 가지고 있는 포드가 없기 때문에 3개의 포드를 생성합니다. 만약 `app: my-nginx-pods-label` 라벨을 가지는 포드를 수동으로 미리 생성해 놓는다면 어떻게 될까요? 다행스럽게도 기존 포드를 인식하고 새로운 생성을 하지 않습니다. 하지만 이렇게 직접 레플리카셋과 **동일한 라벨을 가지는 포드를 직접 생성하는 것은 바람직하지 않습니다.** 

> 레플리카셋 VS 레플레케이션 컨트롤러
> 과거 버전의 쿠버네티스는 레플리카셋이 아닌 레플리케이션 컨트롤러란 오브젝트로 포드 갯수를 유지했습니다. 그러나 레플리케이션 컨트롤러은 더 이상 사용되지 않습니다. 두 오브젝트의 차이 중 하나는 레플리카셋인 표현식 기반의 라벨 셀렉터를 사용할 수 있다는 것 입니다.

## Deployment

레플리카셋만으로도 충분히 마이크로서비스 구조의 컨테이너를 구성하고 서비스할 수 있을것 같지만 실제로 쿠버네티스 운영 환경에서 레플리카셋을 YAML 파일에서 사용하는 경우는 거의 없습니다. 대부분은 레플리카셋과 포드의 정보를 정의하는 디플로이먼트를 사용합니다. 

디플로이먼트는 리플리카셋의 상위 오브젝트이기 때문에 디플로이먼트를 생성하면 그에 따른 레플리카셋도 생성됩니다. 따라서 디플로이먼트를 사용하면 포드나 레플리카셋을 생성할 필요가 없습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: my-nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      name: my-nginx-pod
      labels:
        app: my-nginx
    spec:
      containers:
    - name: nginx
      image: nginx:latest
      ports:
      - containerPort: 80      
```
위의 YAML은 사실 레플리카셋의 YAML파일과 비교하여 큰 변경점이 없습니다. 디플로이먼트를 생성하면 my-nginx-delpoyment라는 이름의 오브젝트가 생성되어 있습니다. READY라는 항목에 3/3 출력을 통해 3개의 포드가 정상 동작했다는 것도 알수 있습니다. 

```
kubectl get deployment
kubectl get replicasets
kubectl get pods
```
위 명령어를 치면 하나의 디플로이먼트를 생성했음에도 그에 대응하는 레플리카셋과 포드가 생성된것을 확인할 수 있습니다. 

이런 디플로이먼트를 사용하는 이유는 바로 애플리케이션의 업데이트와 배포의 편의성 때문입니다. 예를 들어, 애플리케이션을 업데이트 할때 레플리카셋의 변경 사항을 저장하는 리비전(Revision) 정보를 남겨두어 롤백이 가능하고, 무중단 서비스를 위해 포드의 롤링 업데이트 전략을 지정할 수도 있습니다. 

**롤링 업데이트**는 포드 인스턴스를 점진적으로 새로운 것으로 업데이트하여 디플로이먼트 업데이트가 서비스 중단 없이 이루어지도록 합니다. 

디플로이먼트 YAML을 작성하고 아래 명령어를 실행해봅시다.
```yaml
kubectl apply -f deployment-nginx.yaml --record
```
그리고 nginx 버전을 변경하기 위해 아래 명령어를 실행합니다. 참고로 kubectl set image 명령어 대신 YAML 파일을 직접 nginx:1.11로 수정하고 apply 명령어를 적용해도 동일합니다.
```yaml
kubectl set image deployment my-nginx-deployment nginx=niginx:1.11 --record
```

수행 후에 레플리카셋 목록을 출력해 보면 동일한 이름의 두개의 레플리카셋이 생성됩니다. 디플로이먼트는 포드의 정보를 업데이트하면서 새로운 레플리카셋과 포드를 생성하면서 이전 버전의 레플리카셋을 삭제하지 않고 남겨두고 있습니다. 즉 **디플로이먼트는 포드의 정보가 변경될때, 이전 정보를 리비전으로써 보존합니다.** 

만약 롤백을 하고 싶다면 `--to-revision`으로 되돌리려는 리버전 번호를 입력하면 됩니다. 

정리하자면, 디플로이먼트는 여러 리플리카셋을 관리하기 위한 상위 오브젝트입니다. 리플리카셋들의 리비전 관리뿐만 아니라 롤링 업데이트 정책을 사용할 수도 있습니다. 따라서 쿠버네티스에서도 공식적으로 디플로이먼트를 사용할 것을 권장하고 있습니다. 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTYyODgxMDg5LDI2NzEwOTg4MSwtOTA1MT
MyMjQ3XX0=
-->