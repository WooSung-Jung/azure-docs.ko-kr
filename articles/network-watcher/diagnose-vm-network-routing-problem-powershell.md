---
title: VM 네트워크 라우팅 문제 진단-Azure PowerShell
titleSuffix: Azure Network Watcher
description: 이 문서에서는 Azure Network Watcher의 다음 홉 기능을 사용하여 가상 머신 네트워크 라우팅 문제를 진단하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 6569a99ec851da478151665921e7689e1c3488f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020216"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>가상 머신 네트워크 라우팅 문제 진단 - Azure PowerShell

이 문서에서는 VM(가상 머신)을 배포한 다음, IP 주소와 URL로 전송되는 통신을 확인합니다. 통신 오류의 원인 및 해결 방법을 확인합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에 Azure PowerShell `Az` 모듈이 필요 합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.



## <a name="create-a-vm"></a>VM 만들기

VM을 만들려면 먼저 VM이 포함될 리소스 그룹을 만들어야 합니다. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

[New-AzVM](/powershell/module/az.compute/new-azvm)으로 VM을 만듭니다. 이 단계를 실행할 때 자격 증명을 묻는 메시지가 나타납니다. 입력하는 값은 VM에 대한 사용자 이름과 암호로 구성됩니다.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

VM을 만드는 데 몇 분이 걸립니다. VM이 만들어지고 PowerShell에서 출력을 반환할 때까지 나머지 단계를 진행하지 마십시오.

## <a name="test-network-communication"></a>네트워크 통신 테스트

Network Watcher와의 네트워크 통신을 테스트하려면 먼저 테스트하려는 VM이 있는 지역에서 네트워크 감시자를 활성화한 다음, Network Watcher의 다음 홉 기능을 사용하여 통신을 테스트해야 합니다.

## <a name="enable-network-watcher"></a>네트워크 감시자 사용

미국 동부 지역에서 활성화된 네트워크 감시자가 이미 있는 경우 [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher)를 사용하여 네트워크 감시자를 검색합니다. 다음 예에서는 *NetworkWatcherRG* 리소스 그룹에 있는 *NetworkWatcher_eastus* 라는 기존 네트워크 감시자를 검색합니다.

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

미국 동부 지역에서 활성화된 네트워크 감시자가 아직 없는 경우 [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher)를 사용하여 미국 동부 지역에서 네트워크 감시자를 만듭니다.

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>다음 홉 사용

Azure에서는 기본 대상에 대한 경로를 자동으로 만듭니다. 기본 경로를 재정의하는 사용자 지정 경로를 만들 수 있습니다. 경우에 따라 사용자 지정 경로로 인해 통신이 실패할 수 있습니다. VM에서 라우팅을 테스트 하려면 [AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) 명령을 사용 하 여 트래픽이 특정 주소로 대상이 될 때 다음 라우팅 홉을 결정 합니다.

VM에서 www.bing.com 에 대한 IP 주소 중 하나로 아웃바운드 통신을 테스트합니다.

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

몇 초 후에 결과는 **NextHopType** 이 **인터넷** 이며, **RouteTableId** 가 **시스템 경로** 임을 알려줍니다. 이 출력 결과를 통해 대상에 대한 유효한 경로가 있음을 알 수 있습니다.

VM에서 172.31.0.100으로 아웃바운드 통신을 테스트합니다.

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

반환 된 출력에는 **NextHopType** 가 없고 **RouteTableId** 도 **시스템 경로** **임을 알리는** 메시지가 표시 됩니다. 이 결과를 사용하면 대상에 대한 유효한 시스템 경로가 있지만, 대상에 트래픽을 라우팅하는 다음 홉이 없음을 알 수 있습니다.

## <a name="view-details-of-a-route"></a>경로의 세부 정보 보기

라우팅을 추가로 분석 하려면 [AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) 명령을 사용 하 여 네트워크 인터페이스에 대 한 유효 경로를 검토 합니다.

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

다음 텍스트를 포함하는 출력이 반환됩니다.

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

이전 출력에서 볼 수 있듯이 **AddressPrefix** 가 **0.0.0.0/0** 인 경로는 **인터넷** 의 다음 홉을 사용하여 다른 경로의 주소 접두사 내의 주소로 향하지 않는 모든 트래픽을 라우팅합니다. 또한 출력에서 볼 수 있듯이 172.31.0.100 주소를 포함하는 172.16.0.0/12 접두사에 대한 기본 경로가 있더라도 **nextHopType** 은 **없음** 입니다. Azure에서는 172.16.0.0/12에 대한 기본 경로를 만들지만 필요가 있을 때까지 다음 홉 형식을 지정하지 않습니다. 예를 들어, 가상 네트워크의 주소 공간에 172.16.0.0/12 주소 범위를 추가한 경우, Azure는 경로의 **nextHopType** 을 **가상 네트워크** 로 변경합니다. 그런 다음, 검사에서는 **가상 네트워크** 를 **nextHopType** 으로 표시합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 VM을 만들고 VM에서 네트워크 라우팅을 진단했습니다. Azure가 여러 개의 기본 경로를 만들고 두 개의 다른 대상에 대한 라우팅을 테스트했음을 알아보았습니다. [Azure에서 라우팅](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 및 [사용자 지정 경로를 만드는](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route) 방법을 알아봅니다.

아웃바운드 VM 연결의 경우 Network Watcher의 [연결 문제 해결](network-watcher-connectivity-powershell.md) 기능을 사용하여 대기 시간 외에도 VM과 엔드포인트 간의 허용 또는 거부되는 네트워크 트래픽을 확인할 수 있습니다. Network Watcher 연결 모니터 기능을 사용하여 시간에 따라 IP 주소 또는 URL과 같은 VM과 엔드포인트 간의 통신을 모니터링할 수 있습니다. 방법을 알아보려면 [네트워크 연결 모니터링](connection-monitor.md)을 참조하세요.
