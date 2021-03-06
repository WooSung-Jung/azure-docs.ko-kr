---
title: Azure에서 Windows VHD 다운로드
description: Azure Portal을 사용하여 Windows VHD를 다운로드합니다.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: a33b248c18bcbf322a1e2d911453a1c4c087e625
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550521"
---
# <a name="download-a-windows-vhd-from-azure"></a>Azure에서 Windows VHD 다운로드

이 문서에서는 Azure Portal을 사용하여 Azure에서 Windows VHD(가상 하드 디스크) 파일을 다운로드하는 방법을 알아봅니다.

## <a name="optional-generalize-the-vm"></a>선택 사항: VM 일반화

VHD를 [이미지로](tutorial-custom-images.md) 사용 하 여 다른 vm을 만들려면 [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) 를 사용 하 여 운영 체제를 일반화 해야 합니다. 

VHD를 이미지로 사용 하 여 다른 Vm을 만들려면 VM을 일반화 합니다.

1. 아직 수행 하지 않은 경우 [Azure Portal](https://portal.azure.com/)에 로그인 합니다.
2. [VM에 연결](connect-logon.md)합니다. 
3. VM에서 관리자로 명령 프롬프트 창을 엽니다.
4. 디렉터리를 *%windir%\system32\sysprep* 로 변경한 후 sysprep.exe를 실행합니다.
5. 시스템 준비 도구 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력** 을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
6. 종료 옵션에서 **종료** 를 선택한 다음 **확인** 을 클릭 합니다. 


## <a name="stop-the-vm"></a>VM을 중지합니다.

실행 중인 VM에 연결된 경우 Azure에서 VHD는 다운로드할 수 없습니다. VHD를 다운로드하려면 VM을 중지해야 합니다. 

1. Azure Portal에 있는 허브 메뉴에서 **Virtual Machines** 를 클릭합니다.
1. 목록에서 VM을 선택합니다.
1. VM에 대한 블레이드에서 **중지** 를 클릭합니다.


## <a name="generate-download-url"></a>다운로드 URL 생성

VHD 파일을 다운로드하려면 [SAS(공유 액세스 서명)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL을 생성해야 합니다. URL이 생성될 때 만료 시간이 URL에 할당됩니다.

1. VM에 대 한 페이지의 왼쪽 메뉴에서 **디스크** 를 클릭 합니다.
1. VM에 대 한 운영 체제 디스크를 선택 합니다.
1. 디스크에 대 한 페이지의 왼쪽 메뉴에서 **디스크 내보내기** 를 선택 합니다.
1. URL의 기본 만료 시간은 *3600* 초입니다. Windows OS 디스크에 대해 **36000** 로 늘립니다.
1. **URL 생성** 을 클릭합니다.

> [!NOTE]
> Windows Server 운영 체제에 대한 큰 VHD 파일을 다운로드하는 데 충분한 시간을 제공하기 위해 만료 시간은 기본 시간에서 증가되었습니다. Windows Server 운영 체제를 포함하는 VHD 파일은 사용자 연결에 따라 다운로드하는 데 몇 시간이 걸릴 수 있습니다. 데이터 디스크에 대한 VHD를 다운로드하는 경우 기본 시간은 충분합니다. 
> 
> 

## <a name="download-vhd"></a>VHD 다운로드

1. 생성된 URL에서 VHD 파일 다운로드를 클릭합니다.
1. 브라우저에서 **저장** 을 클릭 하 여 다운로드를 시작 해야 할 수도 있습니다. VHD 파일에 대한 기본 이름은 *abcd* 입니다.

## <a name="next-steps"></a>다음 단계

- [Azure로 VHD 파일 업로드](upload-generalized-managed.md) 방법을 알아봅니다. 
- [저장소 계정의 비관리 디스크에서 관리 디스크를 만듭니다](attach-disk-ps.md).
- [PowerShell을 사용 하 여 Azure 디스크를 관리](tutorial-manage-data-disk.md)합니다.
