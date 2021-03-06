---
title: Azure Backup 보호 된 워크 로드 모니터링
description: 이 문서에서는 Azure Portal를 사용 하 여 Azure Backup 작업에 대 한 모니터링 및 알림 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 83ed5af00bb61d7a8929e710b52e60c33c0f479b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559216"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup 워크 로드 모니터링

Azure Backup는 백업 요구 사항 및 인프라 토폴로지 (온-프레미스 vs Azure)를 기반으로 여러 백업 솔루션을 제공 합니다. 모든 백업 사용자 또는 관리자는 모든 솔루션에서 발생 하는 상황을 확인 하 고 중요 한 시나리오에서 알림이 발생할 수 있습니다. 이 문서에서는 Azure Backup 서비스에서 제공 하는 모니터링 및 알림 기능에 대해 자세히 설명 합니다.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 항목

Recovery Services 자격 증명 모음을 통해 모든 백업 항목을 모니터링할 수 있습니다. 자격 증명 모음의 **백업 항목** 섹션으로 이동 하면 자격 증명 모음과 연결 된 각 작업 유형의 백업 항목 수를 제공 하는 보기가 열립니다. 행을 클릭 하면 지정 된 작업 유형의 모든 백업 항목을 나열 하는 상세 보기와 각 항목에 대 한 마지막 백업 상태에 대 한 정보, 사용 가능한 최근 복원 지점 등이 열립니다.

![RS vault 백업 항목](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> DPM을 사용 하 여 Azure에 백업 된 항목의 경우 목록에 DPM 서버를 사용 하 여 보호 된 모든 데이터 원본 (디스크 및 온라인)이 표시 됩니다. 백업 데이터를 보존 하는 데이터 원본에 대 한 보호가 중지 된 경우 데이터 원본은 포털에 계속 나열 됩니다. 데이터 원본의 세부 정보로 이동 하 여 복구 지점이 디스크, 온라인 또는 둘 다에 있는지 확인할 수 있습니다. 또한 온라인 보호를 중지 했지만 데이터가 유지 되는 데이터 원본은 데이터를 완전히 삭제할 때까지 온라인 복구 지점의 청구를 계속 합니다.
>
> 백업 항목이 Recovery Services 자격 증명 모음 포털에 표시 되려면 dpm 버전이 DPM 1807 (5.1.378.0) 또는 DPM 2019 (버전 10.19.58.0 이상) 이어야 합니다.

## <a name="backup-jobs-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 작업

Azure Backup은 Azure Backup으로 보호 되는 워크 로드에 대 한 기본 제공 모니터링 및 경고 기능을 제공 합니다. Recovery Services 자격 증명 모음 설정에서 **모니터링** 섹션은 작성 된 작업 및 경고를 제공 합니다.

![RS 자격 증명 모음 내장 모니터링](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

작업은 백업 구성, 백업, 복원, 삭제 등의 작업을 수행 하는 경우에 생성 됩니다.

다음 Azure Backup 솔루션의 작업이 여기에 표시 됩니다.

- Azure VM 백업
- Azure 파일 백업
- Azure 워크 로드 백업 (예: SQL 및 SAP HANA)
- MARS(Microsoft Azure Recovery Services) 에이전트

System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup 서버 (MABS)의 작업이 표시 되지 않습니다.

> [!NOTE]
> Azure Vm 내에서 SQL 및 SAP HANA 백업 같은 azure 워크 로드에는 상당한 수의 백업 작업이 있습니다. 예를 들어 로그 백업은 15 분 마다 실행할 수 있습니다. 따라서 이러한 DB 워크 로드의 경우 사용자가 트리거한 작업만 표시 됩니다. 예약 된 백업 작업은 표시 되지 않습니다.

## <a name="backup-alerts-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 경고

> [!NOTE]
> 자격 증명 모음에 대 한 경고 보기는 현재 백업 센터에서 지원 되지 않습니다. 해당 자격 증명 모음에 대 한 경고를 보려면 개별 자격 증명 모음으로 이동 해야 합니다.

경고는 사용자가 관련 작업을 수행할 수 있도록 사용자에 게 알림을 제공 하는 경우 주로 발생 합니다. **백업 경고** 섹션에는 Azure Backup 서비스에 의해 생성 된 경고가 표시 됩니다. 이러한 경고는 서비스에서 정의 되며 사용자가 경고를 만들 수 없습니다.

### <a name="alert-scenarios"></a>경고 시나리오

다음 시나리오는 했어야 시나리오로 서비스에 의해 정의 됩니다.

- 백업/복원 실패
- Microsoft Azure Recovery Services (MARS) 에이전트에 대해 백업 했습니다.
- 데이터 삭제로 보호 중지/데이터 삭제로 보호 중지

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>다음 Azure Backup 솔루션의 경고가 여기에 표시 됩니다.

- Azure VM 백업
- Azure 파일 백업
- SQL, SAP HANA 등의 Azure 워크 로드 백업
- MARS(Microsoft Azure Recovery Services) 에이전트

> [!NOTE]
> System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup 서버 (MABS)의 경고가 여기에 표시 되지 않습니다.

### <a name="consolidated-alerts"></a>통합 된 경고

SQL 및 SAP HANA 같은 Azure 워크 로드 백업 솔루션의 경우 로그 백업은 매우 자주 생성 될 수 있습니다 (정책에 따라 최대 15 분 마다). 따라서 로그 백업 실패가 매우 자주 발생 하는 경우도 있습니다 (최대 15 분까지). 이 시나리오에서는 각 실패 발생에 대해 경고가 발생 하는 경우 최종 사용자에 게 과부하가 발생 합니다. 따라서 첫 번째 발생에 대 한 경고를 보내고 이후 실패가 동일한 근본 원인으로 인해 발생 하는 경우 추가 경고가 생성 되지 않습니다. 첫 번째 경고가 실패 횟수로 업데이트 됩니다. 그러나 사용자가 경고를 비활성화 하는 경우 다음 번에 발생 하면 다른 경고가 트리거되고 해당 발생에 대 한 첫 번째 경고로 처리 됩니다. 이는 Azure Backup SQL 및 SAP HANA 백업에 대 한 경고 통합을 수행 하는 방법입니다.

### <a name="exceptions-when-an-alert-is-not-raised"></a>경고가 발생 하지 않는 경우의 예외

오류가 발생 해도 경고가 발생 하지 않는 경우는 몇 가지 예외가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- 사용자가 실행 중인 작업을 명시적으로 취소 함
- 다른 백업 작업이 진행 중 이므로 작업이 실패 합니다 (이전 작업이 완료 될 때까지 기다려야 함).
- 백업 된 Azure VM이 더 이상 존재 하지 않으므로 VM 백업 작업이 실패 합니다.
- [통합 된 경고](#consolidated-alerts)

위의 예외는 이러한 작업 (주로 사용자 트리거됨)의 결과가 portal/PS/CLI 클라이언트에 즉시 표시 된다는 것을 이해 하는 데 사용 됩니다. 따라서 사용자는 즉시 인식 되며 알림이 필요 하지 않습니다.

### <a name="alert-types"></a>경고 유형

경고 심각도에 따라 다음과 같은 세 가지 유형으로 경고를 정의할 수 있습니다.

- **중요**: 원칙적으로 모든 백업 또는 복구 오류 (예약 또는 사용자 트리거)가 경고를 생성 하 고 중요 한 경고로 표시 되며 백업 삭제와 같은 작업을 수행 합니다.
- **경고**: 백업 작업이 성공 하지만 경고가 거의 없으면 경고 경고로 표시 됩니다. 경고 경고는 현재 Azure Backup 에이전트 백업에 대해서만 사용할 수 있습니다.
- **정보**: 현재 Azure Backup 서비스에 의해 생성 된 정보 경고가 없습니다.

## <a name="notification-for-backup-alerts"></a>백업 경고에 대 한 알림

> [!NOTE]
> 알림 구성은 Azure Portal 통해서만 수행할 수 있습니다. PS/CLI/REST API/Azure Resource Manager 템플릿 지원은 지원 되지 않습니다.

경고가 발생 하면 사용자에 게 알림이 제공 됩니다. Azure Backup는 전자 메일을 통해 기본 제공 알림 메커니즘을 제공 합니다. 경고가 생성 될 때 알림을 받을 개별 메일 주소 또는 메일 그룹을 지정할 수 있습니다. 각 경고에 대 한 알림을 받을지 또는 매시간 다이제스트로 그룹화 한 다음 알림을 받을 지를 선택할 수도 있습니다.

![RS Vault 내장 전자 메일 알림](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

알림이 구성 되 면 환영 또는 소개 전자 메일을 받게 됩니다. 이렇게 하면 경고가 발생할 때 Azure Backup이 주소로 전자 메일을 보낼 수 있습니다.<br>

빈도가 매시간 다이제스트로 설정 되 고 경고가 발생 하 고 한 시간 내에 해결 된 경우 예정 된 매시간 다이제스트의 일부가 되지 않습니다.

> [!NOTE]
>
> - **데이터 삭제로 보호 중지** 와 같은 파괴적인 작업을 수행 하면 경고가 발생 하 고 Recovery Services 자격 증명 모음에 대해 알림이 구성 되지 않은 경우에도 전자 메일이 구독 소유자, 관리자 및 공동 관리자에 게 전송 됩니다.
> - 성공한 작업에 대 한 알림을 구성 하려면 [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)을 사용 합니다.

## <a name="inactivating-alerts"></a>비활성화 경고

활성 경고를 비활성화/해결 하려면 비활성화할 경고에 해당 하는 목록 항목을 선택할 수 있습니다. 이렇게 하면 경고에 대 한 자세한 정보를 표시 하는 화면이 열리고 맨 위에 **비활성화** 단추가 표시 됩니다. 이 단추를 선택 하면 경고 상태가 **비활성** 으로 변경 됩니다. 해당 경고에 해당 하는 목록 항목을 마우스 오른쪽 단추로 클릭 하 고 **비활성화** 를 선택 하 여 경고를 비활성화할 수도 있습니다.

![RS Vault 경고 비활성화](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure Backup에 대 한 Azure Monitor 경고 (미리 보기)

또한 Azure Backup는 Azure Monitor를 통해 경고를 제공 하 여 사용자가 백업을 비롯 한 다양 한 Azure 서비스에서 경고 관리에 대해 일관 된 환경을 제공할 수 있도록 합니다. Azure Monitor 경고를 사용 하 여 전자 메일, ITSM, Webhook, 논리 앱 등의 Azure Backup 지원 되는 알림 채널로 경고를 라우팅할 수 있습니다.

현재이 기능은 PostgreSQL 서버, Azure Blob 및 Azure Managed Disks 용 Azure 데이터베이스에 사용할 수 있습니다. 경고는 다음과 같은 시나리오에 대해 생성 되며 백업 자격 증명 모음으로 이동 하 고 **경고** 메뉴 항목을 클릭 하 여 액세스할 수 있습니다.

- 백업 데이터 삭제
- 백업 오류 (백업 실패에 대 한 경고를 가져오려면 미리 보기 포털을 통해 **EnableAzureBackupJobFailureAlertsToAzureMonitor** 이라는 afec 플래그를 등록 해야 합니다.)
- 복원 실패 (복원 실패에 대 한 경고를 받으려면 미리 보기 포털을 통해 **EnableAzureBackupJobFailureAlertsToAzureMonitor** 이라는 afec 플래그를 등록 해야 합니다.)

Azure Monitor 경고에 대 한 자세한 내용은 [Azure의 경고 개요](../azure-monitor/alerts/alerts-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Monitor를 사용 하 여 Azure Backup 작업 모니터링](backup-azure-monitoring-use-azuremonitor.md)