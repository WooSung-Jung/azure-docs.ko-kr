---
title: InfluxData Telegraf agent를 사용 하 여 Linux VM에 대 한 사용자 지정 메트릭 수집
description: Azure에서 Linux VM에 InfluxData Telegraf 에이전트를 배포 하 고 Azure Monitor에 메트릭을 게시 하도록 에이전트를 구성 하는 방법에 대 한 지침을 설명 합니다.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.openlocfilehash: 204124240c6831ebb2c1df436736f475c48d98a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048939"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집

Azure Monitor를 사용하면 애플리케이션 원격 분석, Azure 리소스에서 실행되는 에이전트 또는 외부 모니터링 시스템을 통해서도 사용자 지정 메트릭을 수집할 수 있습니다. 그런 다음, Azure Monitor로 바로 전송할 수 있습니다. 이 문서에서는 Azure의 Linux VM에 [InfluxData](https://www.influxdata.com/) Telegraf 에이전트를 배포하고 메트릭을 Azure Monitor에 게시하도록 에이전트를 구성하는 방법에 대한 지침을 제공합니다. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf 에이전트 

[Telegraf](https://docs.influxdata.com/telegraf/)는 150개 이상의 다양한 원본에서 메트릭을 수집할 수 있는 플러그 인 기반 에이전트입니다. VM에서 실행되는 워크로드에 따라 특수한 입력 플러그 인을 활용하여 메트릭을 수집하도록 에이전트를 구성할 수 있습니다. 예를 들어, MySQL, NGINX, Apache 등이 있습니다. 그런 다음, 에이전트는 출력 플러그 인을 사용하여 선택한 대상에 쓸 수 있습니다. Telegraf 에이전트는 Azure Monitor 사용자 지정 메트릭 REST API와 직접 통합되고 Azure Monitor 출력 플러그 인을 지원합니다. 이 플러그 인을 사용하면 에이전트가 Linux VM에서 워크로드 특정 메트릭을 수집한 다음, 사용자 지정 메트릭으로 Azure Monitor에 전송할 수 있습니다. 

 ![Telegraph 에이전트 개요](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

> [!NOTE]  
> 사용자 지정 메트릭은 모든 지역에서 지원 되지 않습니다. 지원 되는 지역은 [여기](./metrics-custom-overview.md#supported-regions) 에 나열 됩니다.

## <a name="send-custom-metrics"></a>사용자 지정 메트릭 보내기 

이 자습서에서는 Ubuntu 16.04 LTS 운영 체제를 실행하는 Linux VM을 배포합니다. Telegraf 에이전트는 대부분의 Linux 운영 체제에 대해 지원됩니다. Debian 및 RPM 패키지는 모두 [InfluxData 다운로드 포털](https://portal.influxdata.com/downloads)에서 패키지되지 않은 Linux 이진 파일과 함께 사용할 수 있습니다. 추가 설치 지침 및 옵션은 이 [Telegraf 설치 가이드](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/)를 참조하세요. 

[Azure Portal](https://portal.azure.com)에 로그인합니다.

> [!NOTE]  
> 기존 경고 규칙을 마이그레이션하고 기존 Linux 가상 머신을 사용 하려면 가상 컴퓨터의 시스템 할당 id가 **On** 으로 설정 되어 있는지 확인 합니다.

새 Linux VM을 만듭니다. 

1. 왼쪽 탐색 창에서 **리소스 만들기** 옵션을 선택 합니다. 
1. **가상 머신** 을 검색합니다.  
1. **Ubuntu 16.04 LTS** 를 선택하고 **만들기** 를 선택합니다. 
1. VM 이름 (예: **MyTelegrafVM**)을 제공 합니다.  
1. 디스크 유형은 **SSD** 로 그대로 둡니다. 그런 다음 **azureuser** 와 같은 **사용자 이름을** 제공 합니다. 
1. **인증 유형** 으로 **암호** 를 선택 합니다. 나중에 이 VM에 대해 SSH를 수행할 때 사용할 암호를 입력합니다. 
1. **새 리소스 그룹을 만들도록** 선택 합니다. 그런 다음 **Myresourcegroup** 과 같은 이름을 제공 합니다. **위치** 를 선택 합니다. 그런 다음 **확인** 을 선택합니다. 

    ![Ubuntu VM 만들기](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. VM의 크기를 선택합니다. 예를 들어 **컴퓨팅 형식** 또는 **디스크 형식** 으로 필터링할 수 있습니다. 

    ![가상 머신 크기 Telegraph 에이전트 개요](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1.  **네트워크**  >  **네트워크 보안 그룹** 의 설정 페이지에서  >  **공용 인바운드 포트를 선택** 하 고 **HTTP** 및 **SSH (22)** 를 선택 합니다. 나머지는 기본값으로 두고 **확인** 을 선택합니다. 

1. 요약 페이지에서 **만들기** 를 선택하여 VM 배포를 시작합니다. 

1. VM이 Azure Portal 대시보드에 고정됩니다. 배포가 완료되면 VM 요약이 자동으로 열립니다. 

1. VM 창에서 **id** 탭으로 이동 합니다. VM의 시스템 할당 id가 **On** 으로 설정 되어 있는지 확인 합니다. 
 
    ![Telegraf VM ID 미리 보기](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>VM에 연결 

VM과의 SSH 연결을 만듭니다. VM의 개요 페이지에서 **연결** 단추를 선택합니다. 

![Telegraf VM 개요 페이지](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

**가상 머신에 연결** 페이지에서, 22 포트를 통해 DNS 이름으로 연결하는 기본 옵션을 유지합니다. **VM 로컬 계정을 사용 하 여 로그인** 에서 연결 명령이 표시 됩니다. 명령을 복사하는 단추를 선택합니다. 다음 예제에서는 SSH 연결 명령의 형식을 보여줍니다. 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Azure Cloud Shell이나 Windows Ubuntu의 Bash와 같은 셸에 SSH 연결 명령을 붙여넣거나 선택한 SSH 클라이언트를 사용하여 연결을 만듭니다. 

## <a name="install-and-configure-telegraf"></a>Telegraf 설치 및 구성 

VM에 Telegraf Debian 패키지를 설치하려면 SSH 세션에서 다음 명령을 실행합니다. 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegraf의 구성 파일은 Telegraf의 작업을 정의합니다. 기본적으로는 예제 구성 파일은 **/etc/telegraf/telegraf.conf** 경로에 설치됩니다. 구성 파일 예제에는 가능한 모든 입력 및 출력 플러그 인이 나열 됩니다. 그러나 사용자 지정 구성 파일을 만들고 에이전트에서 다음 명령을 실행 하 여 사용 하도록 합니다. 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> 이전 코드는 **cpu** 및 **mem** 의 두 입력 플러그 인만 사용하도록 설정합니다. 시스템에서 실행되는 워크로드에 따라 입력 플러그 인을 더 추가할 수 있습니다. 예를 들어, Docker, MySQL, NGINX 등이 있습니다. 입력 플러그 인의 전체 목록은 **추가 구성** 섹션을 참조하세요. 

마지막으로, 새 구성을 사용하여 에이전트를 시작하려면 다음 명령을 실행하여 해당 에이전트를 강제로 중지하고 시작합니다. 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
이제 에이전트는 지정된 각 입력 플러그 인에서 메트릭을 수집하고 Azure Monitor로 내보냅니다. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Azure Portal에서 Telegraf 메트릭 그리기 

1. [Azure Portal](https://portal.azure.com)을 엽니다. 

1. 새 **모니터** 탭으로 이동 합니다. 그런 다음 **메트릭** 을 선택 합니다.  

     ![모니터 - 메트릭(미리 보기)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. 리소스 선택기에서 VM을 선택합니다.

     ![메트릭 차트](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. **Telegraf/CPU** 네임 스페이스를 선택하고, **usage_system** 메트릭을 선택합니다. 이 메트릭의 차원으로 필터링하거나 차원을 분할하도록 선택할 수 있습니다.  

     ![네임스페이스 및 메트릭 선택](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>추가 구성 

앞의 연습에서는 몇 가지 기본 입력 플러그 인에서 메트릭을 수집 하도록 Telegraf agent를 구성 하는 방법에 대 한 정보를 제공 합니다. Telegraf 에이전트는 추가 구성 옵션을 지 원하는 몇 가지 추가 구성 옵션을 포함 하 여 150 이상의 입력 플러그 인을 지원 합니다. InfluxData는 [지원되는 플러그 인 목록](https://docs.influxdata.com/telegraf/v1.15/plugins/inputs/) 및 [구성 방법](https://docs.influxdata.com/telegraf/v1.15/administration/configuration/)에 대한 지침을 게시합니다.  

또한 이 연습에서는 Telegraf 에이전트를 사용하여 에이전트가 배포된 VM에 대한 메트릭을 내보냈습니다. Telegraf 에이전트를 다른 리소스에 대한 메트릭의 수집기와 전달자로 사용할 수도 있습니다. 다른 Azure 리소스에 대한 메트릭을 내보내도록 에이전트를 구성하는 방법을 알아보려면 [Telegraf에 대한 Azure Monitor 사용자 지정 메트릭 출력](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md)을 참조하세요.  

## <a name="clean-up-resources"></a>리소스 정리 

더 이상 필요 없는 경우 리소스 그룹, 가상 머신 및 모든 관련 리소스를 삭제해도 됩니다. 이렇게 하려면 가상 컴퓨터의 리소스 그룹을 선택 하 고 **삭제** 를 선택 합니다. 삭제할 리소스 그룹의 이름을 확인합니다. 

## <a name="next-steps"></a>다음 단계
- [사용자 지정 메트릭](./metrics-custom-overview.md)에 대해 자세히 알아보세요.