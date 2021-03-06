---
title: HBv3 시리즈-Azure Virtual Machines
description: HBv3 시리즈 Vm에 대 한 사양입니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: a03eac3969e8918c8da20b90d0dcf8b60b39b8ee
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800842"
---
# <a name="hbv3-series"></a>HBv3 시리즈

HBv3 시리즈 Vm은 유체 dynamics, 명시적 및 암시적 유한 요소 분석, 날씨 모델링, 지진 처리, 저수지 시뮬레이션 및 RTL 시뮬레이션 같은 HPC 응용 프로그램에 최적화 되어 있습니다. HBv3 Vm은 최대 120 AMD EPYC™ 7003 시리즈 (Milan) CPU 코어, 448 g b RAM, 하이퍼스레딩을 갖지 않습니다. HBv3 시리즈 Vm은 메모리 대역폭의 350 g b/초를 제공 합니다. 코어 당 최대 32 MB의 L3 캐시, 블록 장치 SSD 성능의 최대 7GB/s, 최대 3.675 GHz의 클록 주파수를 제공 합니다. 

모든 HBv3 시리즈 Vm은 NVIDIA 네트워킹에서 200 g b/sec HDR InfiniBand를 사용 하 여 고성능 컴퓨터 규모의 MPI 작업을 지원 합니다. 이러한 Vm은 최적화 되 고 일관 된 RDMA 성능을 위해 차단 되지 않는 fat 트리에 연결 됩니다. 또한 HDR InfiniBand fabric은 적응 라우팅과 표준 RC 및 UD 전송에 대 한 추가의 DCT (동적 연결 전송)를 지원 합니다. 이러한 기능을 사용 하면 응용 프로그램 성능, 확장성 및 일관성을 향상 시킬 수 있으며, 이러한 기능을 사용 하는 것이 좋습니다.

[Premium Storage](premium-storage-performance.md): 지원 됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원 됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원 되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원 되지 않음<br>
[VM 생성 지원](generation-2.md): 1 세대 및 2 세대<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 서비스 예정<br>
[삭제 되는 OS 디스크](ephemeral-os-disks.md): 지원 되지 않음 <br>
<br>

|크기 |vCPU |프로세서 |메모리(GiB) |메모리 대역폭 (GB/초) |기본 CPU 빈도 (GHz) |모든 코어 빈도 (GHz, 최고) |단일 코어 빈도 (GHz, 최고) |RDMA 성능 (Gb/s) |MPI 지원 |임시 스토리지(GiB) |최대 데이터 디스크 수 |최대 이더넷 vNICs |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |모두 |2 * 960 |32 |8 |
|Standard_HB120-96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |모두 |2 * 960 |32 |8 |
|Standard_HB120-64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |모두 |2 * 960 |32 |8 |
|Standard_HB120-32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |모두 |2 * 960 |32 |8 |
|Standard_HB120-16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |모두 |2 * 960 |32 |8 |

에 대해 자세히 알아보세요.
- [아키텍처 및 VM 토폴로지](./workloads/hpc/hbv3-series-overview.md)
- 지원 되는 OS를 포함 하 여 지원 되는 [소프트웨어 스택](./workloads/hpc/hbv3-series-overview.md#software-specifications)
- HBv3 시리즈 VM의 예상 [성능](./workloads/hpc/hbv3-performance.md)

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크 로드 예제 및 성능 결과에 대해 읽어 보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
- [ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
