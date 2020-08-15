# Service

포드의 IP는 영속적이지 않아서 항상 변할 수 있다는 점을 주의해야 합니다. 여러 디플로이먼트로 하나의 완벽한 서비스로 연동하려면 포드 IP가 아닌 서로를 발견(Discovery)할 수 있는 좋은 방법이 필요합니다. 

디플로이먼트의 YAML의 파일에는 **단지 애플리케이션이 사용할 내부 포트만 정의합니다.** containerPort 항목이 바로 내부에서 사용할 포트번호 입니다. 내부 포트만 정의해서 이 포드가 바로 외부로 노출되는 것은 아닙니다. 이 **포트를 외부로 노출해 사용자들이 접근하거나, 다른 디플로이먼트의 포드들이 내부적으로 접근하려면 서비스**라 불리는 별도의 쿠버네티스 오브젝트를 사용해야 합니다. 

서비스는 포드에 접근하기 위한 규칙을 정의하기 때문에 쿠버네티스에 애플리케이션을 배포하기 위해서는 반드시 알아야할 오브젝트입니다. 서비스의 핵심 기능을 나열하자면 다음과 같습니다. 

* 여러 포드에 쉽게 접근하도록 고유한 이름을 부여
* 여러 포드에 접근할때, 요청을 분산하는 로드 밸런서 기능을 수행
* 클라우드 플랫폼의 로드밸런서, 클러스터 노드의 포트 등을 통해 포드를 외부로 노출

Pod는 서로 독립적인 IP주소를 가집니다. 그리고 서비스는 다수의 IP주소를 묶어 하나의 논리적인 서비스 IP주소를 만들 수 있습니다. 그리고 하나로 묶인 파드셋에 걸쳐서 트래픽을 라우팅하게 됩니다.   

![](https://d33wubrfki0l68.cloudfront.net/b964c59cdc1979dd4e1904c25f43745564ef6bee/f3351/docs/tutorials/kubernetes-basics/public/images/module_04_labels.svg)

레이블과 셀렉터는 쿠버네티스의 인스턴스들에 대해 논리적인 기본 그룹핑 단위입니다. 레이블은 오브젝트의 생성 시점 또는 이후 시점에 붙여질 수 있고 언제든지 수정이 가능합니다. 레이블은 오브젝트들에 붙여진 키/밸류 쌍으로 다양한 방식으로 이용 가능합니다.

```yaml
#Label Selector 
s:app=A, s:app=B
#Label
app=A, app=B
```

## ClusterIP

ClusterIP 타입의 서비스는 쿠버네티스 내부에서만 포드들에 접근할때 사용합니다. 외부로는 포드를 노출하지 않기 때문에 쿠버네티스 내부에서만 사용되는 포드에 적합합니다.

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: hostname-svc-clusterip
spec:
  ports:
    - name: web-port
      port: 8080
      targetPort: 80
  selector:
    app: webserver
  type: ClusterIP
```

spec.selector
: selector 항목은 이 서비스에서 어떠한 라벨을 가지는 포드에 접근이 가능하도록 허가할 것인지 결정합니다. 위 예시에서는 `app: webserver`라는 라벨을 가진 포드들 집합에 접근 가능한 서비스를 생성합니다. 

spec.ports.port
: 생성된 서비스는 쿠버네티스 내부에서만 사용할 수 있는 고유한 ClusterIP를 할당받습니다. port 항목에는 본 서비스 IP에 접근할때 사용할 포트를 정합니다.

spec.ports.targetPort
: selector 항목에서 정의한 라벨에 의해 접근 대상이 된 포드들이 내부적으로 사용하고 있는 포트를 입력합니다. 즉 포드 템플릿에 정의된 containerPort 항목 값과 같은 같으로 설정해야 합니다. 앞서 디플로이먼트 예시에서는 containerPort 포트를 80으로 선언했기 때문에 ports.targetPort 항목을 80으로 설정합니다.

spec.type
: 이 서비스가 어떤 타입인지 나타냅니다. 서비스 종류에 ClusterIP, NodePort, LoadBalaner 등을 설정할 수 있습니다.

위  yaml로 서비스를 생성하면 hostname-svc-clusterip라는 이름의 서비스가 생성됩니다. 이 서비스를 이용해서 포드에 접근하는 방법은 아주 간단합니다. 고유한 CLUSTER-IP의 IP와 PORT 정보로 요청을 보내면 되비다. 이 IP는 쿠버네티스 클러스터 내부에서만 사용하는 IP로, 이 IP를 통해 서비스에 연결된 포드에 접근 가능합니다. 

기본적으로 서비스에 요청을 보내면, **서비스와 연결된 여러 포드에 대해 자동으로 요청이 분산됩니다.** 즉 서비스는 연결된 포드에 대해선 로드 밸런싱을 수행합니다. 

서비스에는 IP 뿐만 아니라 서비스 이름 자체로도 접근가능합니다. 쿠버네티스는 내부적으로 DNS를 구동하고 있으며 포드들은 자동으로 이 DNS를 사용하도록 설정되기 때문입니다. 

실제로 여러 포드가 내부에서 서로를 찾아 연결해야 할때는 **서비스의 이름과 같은 도메인 이름을 사용하는 것이 일반적입니다**. 즉 포드가 서로 상호작용할때 포드 IP를 알 필요가 없으며, 대신 포드와 연결된 서비스를 사용하여 간단히 포드에 접근 가능합니다.

## NodePort

NodePort 타입의 서비스는 클러스터 외부에서도 접근이 가능합니다. 단 NodePort는 모든 노드의 특정 포트를 개방해 서비스에 접근하는 방식입니다. 

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: hostname-svc-nodeport
spec:
  ports:
    - name: web-port
      port: 8080
      targetPort: 80
      # nodePort: 31000
  selector:
    app: webserver
  type: NodePort
```
ClusterIP와 비교했을때 거의 모든 점이 비슷합니다. NodePort는 ClusterIP와 동작 방법이 다른 것일뿐 동일한 서비스 리소스이기 때문에 라벨 셀렉터, 포트 설정등과 같은 기본 항목의 사용법은 동일합니다. 

위 YAML 파일로 NodePort 타입의 서비스를 생성하고 `kubectl get services`를 사용하면 서비스 정보를 볼 수 있습니다. PORT(s) 항목을 보면 8080 포트 뿐만아니라 새로운 포트가 생성된것을 볼 수 있습니다. **이 포트는 모든 노드에서 동일하게 접근할 수 있는 포트입니다.** 즉 클러스터의 모든 노드에서 내부 IP 또는 외부 IP를 통해 해당 포트로 접근하면 서비스를 이용할 수 있습니다. 

각 노드에서 개방되는 포트는 기본적으로 30000~ 32768 포트 중에 랜덤으로 선택되지만, YAML 파일에 nodePort 항목을 정의하면 원하는 포트를 사용할 수도 있습니다. 

사실 NodePort 서비스는 ClusterIP 기능을 포함하고 있습니다. 따라서 NodePort 타입 서비스는 내부 네트워크와 외부 네트워크 양쪽에서 접근이 가능합니다. 

하지만 실제 운영환경에서 NodePort로 서비스를 외부에 제공하는 경우는 많지 않습니다. NodePort에서 포트 번호를 낮은 번호로 설정하기에는 적절하지 않으며 SSL 인증서 적용, 라우팅등과 같은 복잡한 설정을 서비스에 적용하기가 어렵기 때문입니다. 따라서 **NodePort 서비스 그 자체를 외부로 제공하기 보다는 인그레스(Ingress)라고 불리는 쿠버네티스의 오브젝트를 간접적으로 사용하는 경우가 많습니다.**

## LoadBalancer

LoadBalancer 타입 서비스는 서비스 생성과 **동시에 새로운 추가 로드 밸런서를 생성해 포드와 연결합니다.** NodePort를 사용할때는 각 노드의 IP 주소를 알아야 포드에 접근이 가능했습니다. 만약 클라우드 플랫폼을 지원하는 로드밸런서를 사용한다면 도메인 이름과 IP를 클라우드 플랫폼에서 지원 받기 때문에 더욱 쉽게 포드에 접근이 가능합니다. 

일반적으로 AWS, GCP 등과 같은 클라우드 플랫폼 환경에서만 LoadBalancer 타입을 사용할 수 있습니다. 

물론 필요하다면 직접 보유하고 있는 온프레미스 서버에서도 LoadBalancer를 사용할 수 있습니다. 단 쿠버네티스가 이 기능을 직접 지원하는 것은 아니며 MetalLB나 오픈 스택과 같은 특수한 환경을 직접 구축해야 합니다. 

### externalTrafficPolicy

LoadBalancer 타입을 사용하면 외부로 부터 들어온 요청은 각 노드 중 하나로 보내지며, 그 노드에서 다시 포드 중 하나로 전달됩니다. 그런데 이 포드가 요청이 할당된 노드에 있는 포드가 아닌 외부의 다른 노드에 포드로 전달되어 처리가 될 수도 있습니다. 이렇게 불필요한 네트워크 홉(hob)이 발생할 수 있습니다.

요청 전달 메커니즘은 서비스 속성 중에 externalTrafficPolicy에 정의되어 있습니다. `kubectl get -o yaml`을 보면 Cluster로 설정되어 있습니다.

Cluster는 기본적으로 NodePort와 LoadBalancer가 사용하는 방식입니다. 이를 Local로 설정하면 요청 전달시 **노드가 생성한 포드로만 전달이 가능합니다. ** 로컬 노드에 위차한 포드 중 하나로만 요청이 전달됩니다. 즉 추가적인 네트워크 홉이 발생하지 않습니다.

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: hostname-svc-lb-local
spec:
  externalTrafficPolicy: Local
  ports:
    - name: web-port
      port: 80
      targetPort: 80
      # nodePort: 31000
  selector:
    app: webserver
  type: LoadBalancer
```

그렇지만 externalTrafficPolicy를 Local로 설정하는 것이 무조건 좋은 것은 아닙니다. 각 노드에 포드가 고르지 않게 스케줄링이 되어 있다면, 요청이 고르게 분산되지 않을 수도 있기 때문입니다. 

두개의 노드 A,B 가 존재하고 A에 두개의 포드, B에 한개의 포드가 스케쥴링 된 상태라면 노드에서 밸런싱이 잘 되어도 실제 포드가 받는 부하의 양은 동일하지 않습니다.  즉 특정 노드의 포드에 부하가 집중되거나 적어질수도 있으며, 이는 곧 자원 활용률(utilization)측면에서 바람직하지 않을 수도 있습니다. 

Cluster와 Local 모두 장단점이 있기 때문에 뚜렷한 정답은 없습니다. **불필요한 네트워크 홉으로 인한 레이턴시가 중요치 않다면 Cluster를 써도 괜찮습니다. 아니라면 Local을 사용하는게 좋을 수도 있습니다.**

## ExternalName

보통은 앞서 본 ClusterIP, NodePort, LoadBalancer 세 가지만 알아도 충분하지만, 쿠버네티스를 외부 시스템과 연동할때는 ExternalName 타입의 서비스를 사용할수도 있습니다. ExternalName 을 사용하면 **서비스가 외부 도메인을 가리키도록 설정할 수 있습니다.** 

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: externalname-svc
spec:
  type: ExternalName
  externalName: my.database.com
```

예를 들어, 코버네티스의 포드들이 externalname-svc라는 이름으로 요청을 보내는 경우,  쿠버네티스의 DNS는 my.databse.com으로 접근하도록 CNAME 레코드를 반환합니다. 즉 externalname-svc로 요청을 보내면 my.database.com에 접근하게 도비니다. ExternalName 서비스는 코버네티스와 별개로 존재하는 레거시 시스템에 연동해야 하는 상황에서 유용하게 사용할 수 있습니다. CNAME 레코드는 Canonical Name의 줄임말로, 도메인을 가리키는 다른 이름입니다. 

# Ingress

Ingress는 사전적으로는 입구라는 의미이며 일반적으로 외부에서 내부로 향하는 것을 말합니다. 인그레스 트래픽은 외부에서 서버로 유입되는 트래픽을 의미하며 인그레스 네트워크는 인그레스 트래픽을 처리하기 위한 네트워크를 의미합니다. 

이전

다양한 웹 애플리케이션을 하나의 로드 밸런서로 서비스하기 위해 Ingress를 사용합니다. 웹 애플리케이션을 배포하는 과정을 보면 외부에서 직접 접근할 수 없도록 애플리케이션을 내부망에 설치하고 외부에서 접근이 가능한  `ALB`나  `Nginx`,  `Apache`를 프록시 서버로 활용합니다. 프록시 서버는 도메인과 Path 조건에 따라 등록된 서버로 요청을 전달하는데 서버가 바뀌거나 IP가 변경되면 매번 설정을 수정해줘야 합니다. 쿠버네티스의 Ingress는 이를 자동화하면서 기존 프록시 서버에서 사용하는 설정을 거의 그대로 사용할 수 있습니다. 새로운 도메인을 추가하거나 업로드 용량을 제한하기 위해 일일이 프록시 서버에 접속하여 설정할 필요가 없습니다.

하나의 클러스터에 여러 개의 Ingress 설정을 할 수 있어 관리자 접속용 Ingress와 일반 접속용 Ingress를 따로 관리할 수 있습니다.





> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyNjk5ODM2MiwxMDMwOTc2MTgyLDc5MT
Y5MTc2NywtMTM5NjcwODgyMSwtMzU3ODM2OTIsLTEwMDAwNDA2
OTZdfQ==
-->