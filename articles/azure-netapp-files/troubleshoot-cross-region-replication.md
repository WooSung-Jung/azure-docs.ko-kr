---
title: Azure NetApp Files에 대 한 지역 간 복제 문제 해결 | Microsoft Docs
description: Azure NetApp Files에 대 한 지역 간 복제 문제를 해결 하는 데 도움이 될 수 있는 오류 메시지 및 해결 방법에 대해 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: d3d944646689e9e6189b0343e8bf67c8fb0abcbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590928"
---
# <a name="troubleshoot-cross-region-replication"></a>지역 간 복제 문제 해결

이 문서에서는 Azure NetApp Files에 대 한 지역 간 복제 문제를 해결 하는 데 도움이 될 수 있는 오류 메시지 및 해결 방법을 설명 합니다. 

## <a name="errors-creating-replication"></a>복제 생성 오류  

|     오류 메시지    |     해결 방법    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     이미 데이터 복제 관계에 있는 원본 볼륨이 있는 복제는 만들 수 없습니다.    |
|     `Peered region   '{0}' is not accepted`    |     피어 링 지역 간에 복제를 만들려고 합니다.    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     원격 리소스 ID가 볼륨 리소스 ID 인지 확인 합니다.    |

## <a name="errors-authorizing-volume"></a>볼륨 권한 부여 오류  

|     오류 메시지    |     해결 방법    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     `RemoteResourceID`가 UI 또는 API 요청에서 누락 되었거나 잘못 되었습니다 (오류 메시지 수정).    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     `RemoteResourceID`가 UI 또는 API 요청에서 누락 되었거나 잘못 되었습니다.    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     `RemoteResourceID`사용자에 대해이 올바른지 또는 존재 하는지 확인 합니다.    |
|     `Remote volume   '{0}' is not configured for replication`    |     대상 볼륨이 데이터 보호 볼륨이 아닙니다.    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     데이터 보호 볼륨의 원격 리소스 ID에이 원본 볼륨이 없습니다 (잘못 된 원본 ID를 입력 함).    |
|     `The   destination volume replication creation failed (message: {0})`    |     이 오류는 서버 오류를 나타냅니다.   지원 사이트에 문의하십시오.    |

## <a name="errors-deleting-replication"></a>복제 삭제 오류

|     오류 메시지    |     해결 방법    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     복제가 중단 되었거나 초기화 되지 않고 유휴 상태 (초기화 실패) 인지 확인 합니다.    |
|     `Cannot delete   source replication`    |     원본 쪽에서 복제를 삭제할 수 없습니다. 대상 쪽에서 복제를 삭제 하 고 있는지 확인 합니다.    |

## <a name="errors-deleting-volume"></a>볼륨 삭제 오류

|     오류 메시지    |     해결 방법    |
|-|-|
| `Volume is a member of an active volume replication relationship`  |  볼륨을 삭제 하기 전에 복제를 삭제 합니다. [복제 삭제](cross-region-replication-delete.md)를 참조 하세요. 이 작업을 수행 하려면 볼륨의 복제를 삭제 하기 전에 피어 링을 해제 해야 합니다. |
| `Volume with replication cannot be deleted`  |  볼륨을 삭제 하기 전에 복제를 삭제 합니다. [복제 삭제](cross-region-replication-delete.md)를 참조 하세요. 이 작업을 수행 하려면 볼륨의 복제를 삭제 하기 전에 피어 링을 해제 해야 합니다. 

## <a name="errors-resyncing-volume"></a>볼륨 다시 동기화 오류

|     오류 메시지    |     해결 방법    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     볼륨 복제가 "손상 됨" 상태 인지 확인 합니다.    |

## <a name="errors-deleting-snapshot"></a>스냅숏 삭제 오류 

|     오류 메시지    |     해결 방법    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     이 스냅숏을 삭제 하려면 볼륨의 복제가 손상 되었는지 확인 합니다.    |
|     `Cannot delete   volume replication generated snapshot`    |     복제 기준 스냅숏 삭제는 허용 되지 않습니다.    |

## <a name="errors-resizing-volumes"></a>볼륨 크기 조정 오류

|     오류 메시지    |     해결 방법    |
|-|-|
|   오류가 발생 하 여 원본 볼륨의 크기를 조정 하지 못했습니다. `"PoolSizeTooSmall","message":"Pool size too small for total volume size."`  |  지역 간 복제의 원본 볼륨과 대상 볼륨 모두의 용량 풀에 충분 한 여유가 있는지 확인 합니다. 원본 볼륨의 크기를 조정 하면 대상 볼륨의 크기가 자동으로 조정 됩니다. 그러나 대상 볼륨을 호스트 하는 용량 풀에 충분 한 공간이 없는 경우 원본 볼륨과 대상 볼륨의 크기를 모두 조정 하지 못합니다. 자세한 내용은 [지역 간 복제 대상 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume) 을 참조 하세요.   |

## <a name="next-steps"></a>다음 단계  

* [지역 간 복제](cross-region-replication-introduction.md)
* [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)
* [볼륨 복제 만들기](cross-region-replication-create-peering.md)
* [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)
* [재해 복구 관리](cross-region-replication-manage-disaster-recovery.md)
* [지역 간 복제 대상 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)
