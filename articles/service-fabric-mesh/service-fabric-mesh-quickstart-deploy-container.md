---
title: 빠른 시작 - Azure Service Fabric Mesh에 Hello World 배포
description: 이 빠른 시작에서는 Azure Service Fabric Mesh에 Service Fabric Mesh 애플리케이션을 배포하는 방법을 보여줍니다.
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: quickstart
ms.openlocfilehash: c89cc1972a554cac85ce2a258873f6c810e45167
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217723"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>빠른 시작: Service Fabric Mesh에 Hello World 배포

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

[Service Fabric Mesh](service-fabric-mesh-overview.md)를 통해 가상 머신을 프로비전 할 필요 없이 Azure에서 마이크로 서비스 애플리케이션을 쉽게 만들고 관리할 수 있습니다. 이 빠른 시작에서는 Azure에 Hello World 애플리케이션을 만들고 인터넷에 노출합니다. 이 작업은 단일 명령으로 완료됩니다. 몇 분 안에 브라우저에 이 보기가 표시됩니다.

![브라우저의 Hello World 앱][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Azure 계정이 아직 없는 경우 시작하기 전에 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI 설정 
Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 빠른 시작을 완료할 수 있습니다. 다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
Azure에 로그인하고 구독을 선택합니다.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>리소스 그룹 만들기
애플리케이션을 배포할 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하고 이 단계를 건너뛸 수 있습니다. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>애플리케이션 배포

>[!NOTE]
> 2020년 11월 2일부터 Docker Free 계획 계정에서 Docker Hub에 대한 익명 및 인증된 요청에 [다운로드 속도 제한이 적용](https://docs.docker.com/docker-hub/download-rate-limit/)되며 IP 주소에 의해 적용됩니다. 
> 
> 이러한 템플릿은 Docker Hub의 공용 이미지를 사용합니다. 요금이 제한될 수 있다는 점에 유의하세요. 자세한 내용은 [Docker Hub를 사용하여 인증](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)을 참조하세요.

`az mesh deployment create` 명령을 사용하여 리소스 그룹에 애플리케이션을 만듭니다.  다음을 실행합니다.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

이전 명령은 [linux.json 템플릿](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json)을 사용하여 Linux 애플리케이션을 배포합니다. Windows 애플리케이션을 배포하려면 [windows.json 템플릿](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json)을 사용합니다. Windows 컨테이너 이미지가 Linux 컨테이너 이미지보다 크므로 배포하는 데 시간이 더 걸릴 수 있습니다.

이 명령은 아래 표시되는 JSON 코드 조각을 생성합니다. JSON 출력의 ```outputs``` 섹션에서 ```publicIPAddress``` 속성을 복사합니다.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

이 정보는 ARM 템플릿의 ```outputs``` 섹션에서 제공됩니다. 아래와 같이 이 섹션에서는 공용 IP 주소를 가져올 게이트웨이 리소스를 참조합니다. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>애플리케이션을 엽니다.
애플리케이션이 성공적으로 배포되면 CLI 출력의 서비스 엔드포인트에 대한 공용 IP 주소를 복사합니다. 웹 브라우저에서 IP 주소를 엽니다. Azure Service Fabric Mesh 로고가 있는 웹 페이지가 표시됩니다.

## <a name="check-the-application-details"></a>애플리케이션 세부 정보 확인
`az mesh app show` 명령을 사용하여 애플리케이션 상태를 확인할 수 있습니다. 이 명령은 수행할 수 있는 유용한 정보를 제공합니다.

이 빠른 시작에 대한 애플리케이션 이름은 `helloWorldApp`이며 애플리케이션에 대한 세부 정보를 수집하기 위해 다음 명령을 실행합니다.

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>애플리케이션 로그 보기

`az mesh code-package-log get` 명령을 사용하여 배포된 애플리케이션에 대한 로그를 검사합니다.

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션을 삭제할 준비가 되었을 때 [az group delete][az-group-delete] 명령을 실행하여 포함된 리소스 그룹, 애플리케이션 및 네트워크 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

Service Fabric Mesh 애플리케이션을 만들고 배포하는 방법을 자세히 알아보려면 자습서를 계속 진행합니다.
> [!div class="nextstepaction"]
> [다중 서비스 웹 애플리케이션 만들기 및 배포](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: /cli/azure/install-azure-cli