---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 33c8e7938e3b142e1af932e550c16770355babb8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "77594157"
---
```powershell
kubectl describe pod -l "app=voting-analytics, version=1.0" -n voting | Select-String -Pattern "istio-proxy:|voting-analytics:" -Context 0,2
```

`istio-proxy`컨테이너는 다음 예제 출력과 같이 구성 요소와의 네트워크 트래픽을 관리 하는 Istio에 의해 자동으로 삽입 됩니다.

```console
>   voting-analytics:
      Container ID:   docker://35efa1f31d95ca737ff2e2229ab8fe7d9f2f8a39ac11366008f31287be4cea4d
      Image:          mcr.microsoft.com/aks/samples/voting/analytics:1.0
>   istio-proxy:
      Container ID:  docker://1fa4eb43e8d4f375058c23cc062084f91c0863015e58eb377276b20c809d43c6
      Image:         docker.io/istio/proxyv2:1.3.2
```