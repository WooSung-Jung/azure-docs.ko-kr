---
title: 자격 증명 모음에서 여러 SQL Server VM 백업
description: 이 문서에서는 Recovery Services 자격 증명 모음에서 Azure Backup를 사용 하 여 Azure virtual machines의 SQL Server 데이터베이스를 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 798dc81012ad968c3ecc287717240513a08a1349
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "105567216"
---
# <a name="back-up-multiple-sql-server-vms-from-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 여러 SQL Server Vm 백업

SQL Server 데이터베이스는 낮은 RPO(복구 지점 목표)와 장기 보존이 필요한 중요한 워크로드입니다. [Azure Backup](backup-overview.md)을 사용하여 Azure VM(가상 머신)에서 실행되는 SQL Server 데이터베이스를 백업할 수 있습니다.

이 문서에서는 Azure VM에서 실행되는 SQL Server 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업하는 방법을 보여 줍니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 자격 증명 모음을 만들고 구성합니다.
> * 데이터베이스를 검색하고 백업을 설정합니다.
> * 데이터베이스에 대한 자동 보호를 설정합니다.

>[!NOTE]
>**Azure VM의 SQL Server 일시 삭제 및 Azure VM 워크로드의 SAP HANA 일시 삭제** 는 이제 미리 보기로 제공됩니다.<br>
>미리 보기에 가입하려면 AskAzureBackupTeam@microsoft.com으로 문의해 주세요.

## <a name="prerequisites"></a>사전 요구 사항

SQL Server 데이터베이스를 백업하기 전에 다음 조건을 확인하세요.

1. SQL Server 인스턴스를 호스트하는 VM과 동일한 지역 및 구독에서 [Recovery Services 자격 증명 모음](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)을 식별하거나 만듭니다.
1. VM에 [네트워크 연결](backup-sql-server-database-azure-vms.md#establish-network-connectivity)이 있는지 확인합니다.
1. SQL Server 데이터베이스의 이름이 [Azure Backup 데이터베이스 명명 지침](#database-naming-guidelines-for-azure-backup)에 따라 지정되었는지 확인합니다.
1. SQL Server VM 이름 및 리소스 그룹 이름의 결합 된 길이가 Azure Resource Manager Vm (또는 클래식 Vm의 77 문자)에 대해 84 자를 초과 하지 않는지 확인 합니다. 이 제한은 서비스에서 일부 문자를 예약했기 때문에 발생합니다.
1. 데이터베이스에 사용할 수 있는 다른 백업 솔루션이 없는지 확인합니다. 데이터베이스를 백업하기 전에 다른 모든 SQL Server 백업을 사용하지 않도록 설정합니다.
1. SQL Server 2008 R2 또는 SQL Server 2012를 사용 하는 경우 [여기](https://support.microsoft.com/help/2697983/kb2697983-fix-an-incorrect-value-is-stored-in-the-time-zone-column-of)에 설명 된 대로 백업에 대 한 표준 시간대 문제가 발생할 수 있습니다. 위에서 설명한 표준 시간대 관련 문제를 방지 하기 위해 최신 누적 업데이트를 사용 하 고 있는지 확인 합니다. Azure VM에서 SQL Server 인스턴스에 업데이트를 적용 하는 것이 불가능 한 경우 가상 머신에서 표준 시간대에 대 한 DST (일광 절약 시간)를 사용 하지 않도록 설정 합니다.

> [!NOTE]
> Azure VM과 이 VM에서 실행되는 SQL Server 데이터베이스에 대한 Azure Backup을 충돌 없이 사용하도록 설정할 수 있습니다.

### <a name="establish-network-connectivity"></a>네트워크 연결 설정

모든 작업을 실행하려면 SQL Server VM을 Azure Backup 서비스, Azure Storage 및 Azure Active Directory에 연결해야 합니다. 프라이빗 엔드포인트를 사용하거나 필요한 공용 IP 주소 또는 FQDN에 대한 액세스를 허용하면 됩니다. 필요한 Azure 서비스에 대한 적절한 연결을 허용하지 않으면 데이터베이스 검색, 백업 구성, 백업 수행, 데이터 복원과 같은 작업 시 오류가 발생할 수 있습니다.

다음 표에는 연결을 설정하는 데 사용할 수 있는 여러 가지 방법이 나와 있습니다.

| **옵션**                        | **장점**                                               | **단점**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 프라이빗 엔드포인트                 | 가상 네트워크 내 개인 IP를 통한 백업 허용  <br><br>   네트워크 및 자격 증명 모음에 대한 세부적인 제어 지원 | 표준 프라이빗 엔드포인트 [비용](https://azure.microsoft.com/pricing/details/private-link/) 발생 |
| NSG 서비스 태그                  | 범위 변경이 자동으로 병합되어 관리가 더 쉬움   <br><br>   추가 비용 없음 | NSG에만 사용할 수 있음  <br><br>    전체 서비스에 대한 액세스 제공 |
| Azure Firewall FQDN 태그          | 필요한 FQDN이 자동으로 관리되어 관리가 더 쉬움 | Azure Firewall하고만 함께 사용할 수 있음                         |
| 서비스 FQDN/IP에 대한 액세스 허용 | 추가 비용 없음   <br><br>  모든 네트워크 보안 어플라이언스 및 방화벽과 함께 작동 | 광범위한 IP 또는 FQDN 세트에 액세스해야 할 수 있음   |
| HTTP 프록시 사용                 | VM에 대한 인터넷 액세스의 단일 지점                       | 프록시 소프트웨어로 VM을 실행하기 위해 추가 비용이 있음         |

이러한 옵션을 사용하는 방법에 대한 자세한 내용은 아래에 나와 있습니다.

#### <a name="private-endpoints"></a>프라이빗 엔드포인트

프라이빗 엔드포인트를 사용하면 가상 네트워크 내의 서버에서 안전하게 Recovery Services 자격 증명 모음에 연결할 수 있습니다. 프라이빗 엔드포인트는 VNET 주소 공간의 IP를 자격 증명 모음에 사용합니다. 가상 네트워크 내의 리소스와 자격 증명 모음 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크를 통해 이동합니다. 이렇게 하면 퍼블릭 인터넷에서 공개되지 않습니다. Azure Backup의 프라이빗 엔드포인트에 대한 자세한 내용은 [여기](./private-endpoints.md)를 참조하세요.

#### <a name="nsg-tags"></a>NSG 태그

NSG(네트워크 보안 그룹)를 사용하는 경우 *AzureBackup* 서비스 태그를 사용하여 Azure Backup에 대한 아웃바운드 액세스를 허용하세요. Azure Backup 태그 외에도 Azure AD(*AzureActiveDirectory*) 및 Azure Storage(*Storage*)에 대해 유사한 [NSG 규칙](../virtual-network/network-security-groups-overview.md#service-tags)을 만들어 인증 및 데이터 전송에 대한 연결을 허용해야 합니다.  다음 단계에서는 Azure Backup 태그에 대한 규칙을 만드는 프로세스에 대해 설명합니다.

1. **모든 서비스** 에서 **네트워크 보안 그룹** 으로 이동하여 네트워크 보안 그룹을 선택합니다.

1. **설정** 아래에서 **아웃바운드 보안 규칙** 을 선택합니다.

1. **추가** 를 선택합니다. [보안 규칙 설정](../virtual-network/manage-network-security-group.md#security-rule-settings)에 설명된 대로 새 규칙을 만드는 데 필요한 세부 정보를 모두 입력합니다. **대상** 옵션이 *서비스 태그* 로 설정되고 **대상 서비스 태그** 가 *AzureBackup* 으로 설정되어 있는지 확인합니다.

1. **추가** 를 선택하여 새로 만든 아웃바운드 보안 규칙을 저장합니다.

마찬가지로, Azure Storage 및 Azure AD에 대한 NSG 아웃바운드 보안 규칙을 만들 수 있습니다.

#### <a name="azure-firewall-tags"></a>Azure Firewall 태그

Azure Firewall을 사용하는 경우 *AzureBackup* [Azure Firewall FQDN 태그](../firewall/fqdn-tags.md)를 사용하여 애플리케이션 규칙을 만듭니다. 이는 Azure Backup에 대한 모든 아웃바운드 액세스를 허용합니다.

#### <a name="allow-access-to-service-ip-ranges"></a>서비스 IP 범위에 대한 액세스 허용

서비스 IP에 대한 액세스를 허용하도록 선택할 경우 사용할 수 있는 JSON 파일의 IP 범위를 [여기](https://www.microsoft.com/download/confirmation.aspx?id=56519)에서 참조하세요. Azure Backup, Azure Storage 및 Azure Active Directory에 해당하는 IP에 대한 액세스를 허용해야 합니다.

#### <a name="allow-access-to-service-fqdns"></a>서비스 FQDN에 대한 액세스 허용

다음 FQDN을 사용하여 서버에서 필요한 서비스에 액세스하도록 허용할 수도 있습니다.

| 서비스    | 액세스할 도메인 이름                             | 포트
| -------------- | ------------------------------------------------------------ | ---
| Azure Backup  | `*.backup.windowsazure.com`                             | 443
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` | 443
| Azure AD      | [이 문서](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)에 따라 섹션 56 및 59에서 FQDN에 대한 액세스 허용 | 해당 하는 경우

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>HTTP 프록시 서버를 사용하여 트래픽 라우팅

Azure VM에서 SQL Server 데이터베이스를 백업하는 경우 VM의 백업 확장에서 HTTPS API를 사용하여 관리 명령을 Azure Backup에 보내고 데이터를 Azure Storage에 보냅니다. 백업 확장도 인증에 Azure AD를 사용합니다. HTTP 프록시를 통해 이 세 가지 서비스에 대한 백업 확장 트래픽을 라우팅합니다. 필요한 서비스에 대한 액세스를 허용하려면 위에서 언급한 IP 및 FQDN 목록을 사용하세요. 인증된 프록시 서버는 지원되지 않습니다.

### <a name="database-naming-guidelines-for-azure-backup"></a>Azure Backup 데이터베이스 명명 지침

데이터베이스 이름에 다음 요소를 사용하지 마세요.

* 선행 및 후행 공백
* 후행 느낌표(!)
* 닫는 대괄호(])
* 세미콜론 ';'
* 슬래시 '/'

지원되지 않는 문자에 별칭을 사용할 수는 있지만 사용하지 않는 것이 좋습니다. 자세한 내용은 [테이블 서비스 데이터 모델 이해](/rest/api/storageservices/understanding-the-table-service-data-model)를 참조하세요.

>[!NOTE]
>이름에 "+" 또는 "&"와 같은 특수 문자가 포함 된 데이터베이스에 대 한 **보호 구성** 작업은 지원 되지 않습니다. 데이터베이스 이름을 변경하거나 이러한 데이터베이스를 성공적으로 보호할 수 있는 **자동 보호** 를 사용할 수 있습니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server 데이터베이스 검색

VM에서 실행되는 데이터베이스를 검색하는 방법:

1. [Azure Portal](https://portal.azure.com)에서 데이터베이스를 백업하는 데 사용하는 Recovery Services 자격 증명 모음을 엽니다.

2. **Recovery Services 자격 증명 모음** 대시보드에서 **Backup** 을 선택합니다.

   ![Backup을 선택하여 백업 목표 메뉴 열기](./media/backup-azure-sql-database/open-backup-menu.png)

3. **백업 목표** 에서 **작업이 실행되는 위치** 를 **Azure** 로 설정합니다.

4. **백업할 항목** 에서 **Azure VM의 SQL Server** 를 선택합니다.

    ![백업을 위해 Azure VM에서 SQL Server 선택](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. **백업 목표** > **VM에서 DB 검색** 에서 **검색 시작** 을 선택하여 구독에서 보호되지 않은 가상 머신을 검색합니다. 구독에서 보호되지 않은 VM의 수에 따라 검색하는 데 약간의 시간이 걸릴 수 있습니다.

   * 검색 후에는 보호되지 않은 VM이 이름 및 리소스 그룹별로 나열된 목록에 표시됩니다.
   * VM이 예상대로 나열되지 않으면 자격 증명 모음에 이미 백업되어 있는지 확인합니다.
   * 여러 VM은 동일한 이름을 사용할 수 있지만 서로 다른 리소스 그룹에 속합니다.

     ![VM의 DB 검색 중에는 백업이 보류됩니다.](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. VM 목록에서 SQL Server 데이터베이스가 실행되는 VM, **DB 검색** 을 차례로 선택합니다.

7. **알림** 에서 데이터베이스 검색을 추적합니다. 이 작업에 필요한 시간은 VM 데이터베이스의 수에 따라 달라집니다. 선택한 데이터베이스가 검색되면 성공 메시지가 표시됩니다.

    ![배포 성공 메시지](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup은 VM의 모든 SQL Server 데이터베이스를 검색합니다. 검색하는 동안 백그라운드에서 수행되는 작업은 다음과 같습니다.

    * Azure Backup에서 워크로드 백업용 자격 증명 모음에 VM을 등록합니다. 등록된 VM의 모든 데이터베이스는 이 자격 증명 모음에만 백업할 수 있습니다.
    * Azure Backup에서 AzureBackupWindowsWorkload 확장을 VM에 설치합니다. 에이전트는 SQL 데이터베이스에 설치되지 않습니다.
    * Azure Backup에서 NT Service\AzureWLBackupPluginSvc 서비스 계정을 VM에 만듭니다.
      * 모든 백업 및 복원 작업에는 서비스 계정이 사용됩니다.
      * NT Service\AzureWLBackupPluginSvc에는 SQL sysadmin 권한이 필요합니다. Marketplace에서 만든 모든 SQL Server VM에는 SqlIaaSExtension이 설치되어 있습니다. AzureBackupWindowsWorkload 확장은 SQLIaaSExtension을 사용하여 필요한 권한을 자동으로 확보합니다.
    * Marketplace에서 VM을 만들지 않은 경우 또는 SQL 2008 및 2008 R2를 사용 중인 경우 VM에 SqlIaaSExtension이 설치되어 있지 않을 수 있으며, UserErrorSQLNoSysAdminMembership 오류 메시지와 함께 검색 작업이 실패합니다. 이 문제를 해결하려면 [VM 권한 설정](backup-azure-sql-database.md#set-vm-permissions)의 지침을 따르세요.

        ![VM 및 데이터베이스 선택](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>백업 구성  

1. **백업 목표** > **2단계: 백업 구성** 에서 **백업 구성** 을 선택합니다.

   ![백업 구성 선택](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

1. **리소스 추가** 를 선택 하 여 등록 된 모든 가용성 그룹 및 독립 실행형 SQL Server 인스턴스를 확인 합니다.

    ![리소스 추가 선택](./media/backup-azure-sql-database/add-resources.png)

1. **백업할 항목 선택** 화면에서 행 왼쪽에 있는 화살표를 선택 하 여 해당 인스턴스 또는 Always On 가용성 그룹에 있는 보호 되지 않은 모든 데이터베이스의 목록을 확장 합니다.

    ![백업할 항목 선택](./media/backup-azure-sql-database/select-items-to-backup.png)

1. 보호하려는 모든 데이터베이스, **확인** 을 차례로 선택합니다.

   ![데이터베이스 보호](./media/backup-azure-sql-database/select-database-to-protect.png)

   백업 로드를 최적화하기 위해 Azure Backup은 한 백업 작업의 최대 데이터베이스 수를 50개로 설정합니다.

     * 50개가 넘는 데이터베이스를 보호하려면 여러 백업을 구성합니다.
     * 전체 인스턴스 또는 Always On 가용성 그룹을 [사용하도록 설정](#enable-auto-protection)하려면 **AUTOPROTECT** 드롭다운 목록에서 **켜기** 를 선택한 후 **확인** 을 선택합니다.

         > [!NOTE]
         > [자동 보호](#enable-auto-protection) 기능은 모든 기존 데이터베이스에 동시에 보호를 사용하도록 설정할 뿐 아니라 해당 인스턴스 또는 가용성 그룹에 추가되는 새 데이터베이스도 자동으로 보호합니다.  

1. **백업 정책을** 정의 합니다. 다음 방법 중 하나를 수행할 수 있습니다.

   * 기본 정책을 *HourlyLogBackup* 로 선택 합니다.
   * 이전에 SQL용으로 만든 기존 백업 정책을 선택합니다.
   * RPO(복구 지점 목표) 및 보존 범위를 기반으로 새 정책을 정의합니다.

     ![백업 정책 선택](./media/backup-azure-sql-database/select-backup-policy.png)

1. **백업 사용** 을 선택 하 여 **보호 구성** 작업을 제출 하 고 포털의 **알림** 영역에서 구성 진행률을 추적 합니다.

   ![구성 진행률 추적](./media/backup-azure-sql-database/track-configuration-progress.png)

### <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행되는 시기와 유지되는 기간을 정의합니다.

* 정책은 자격 증명 모음 수준에서 만들어집니다.
* 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.
* 백업 정책을 만드는 경우 매일 전체 백업이 기본값입니다.
* 차등 백업을 추가할 수 있지만 전체 백업이 매주 발생하도록 구성하는 경우에만 가능합니다.
* [다양한 유형의 백업 정책](backup-architecture.md#sql-server-backup-types)에 대해 알아봅니다.

백업 정책을 만들려면:

1. 자격 증명 모음에서 **백업 정책** > **추가** 를 선택합니다.
1. **추가** 에서 **Azure VM의 SQL Server** 를 선택하여 정책 유형을 정의합니다.

   ![새 백업 정책에 대한 정책 유형 선택](./media/backup-azure-sql-database/policy-type-details.png)

1. **정책 이름** 에 새 정책의 이름을 입력합니다.

    ![정책 이름 입력](./media/backup-azure-sql-database/policy-name.png)

1. **전체 백업** 에 해당 하는 **편집** 링크를 선택 하 여 기본 설정을 수정 합니다.

   * **백업 빈도** 를 선택 합니다. **매일** 또는 **매주** 를 선택합니다.
   * **매일** 의 경우 백업 작업이 시작될 때 시간과 표준 시간대를 선택합니다. 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.

     ![새 백업 정책 필드](./media/backup-azure-sql-database/full-backup-policy.png)  

1. **보존 범위** 에는 모든 옵션이 기본적으로 선택되어 있습니다. 사용하지 않으려는 보존 범위 제한을 모두 선택 취소한 후 사용할 간격을 설정합니다.

    * 모든 백업 유형(전체, 차등 및 로그)의 최소 보존 기간은 7일입니다.
    * 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
    * 주별 보존 범위 및 주별 보존 설정을 기반으로 특정 날짜에 대한 백업에 태그가 지정되어 유지됩니다.
    * 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.

       ![보존 범위 간격 설정](./media/backup-azure-sql-database/retention-range-interval.png)

1. **확인** 을 선택 하 여 전체 백업에 대 한 설정을 적용 합니다.
1. **차등 백업** 에 해당 하는 **편집** 링크를 선택 하 여 기본 설정을 수정 합니다.

    * **차등 백업 정책** 에서 **사용** 을 선택하여 빈도 및 보존 컨트롤을 엽니다.
    * 하루에 하나의 차등 백업만 트리거할 수 있습니다. 차등 백업은 전체 백업과 동일한 날에 트리거할 수 없습니다.
    * 차등 백업은 최대 180일 동안 보존될 수 있습니다.
    * Master 데이터베이스에 대 한 차등 백업은 지원 되지 않습니다.

      ![차등 백업 정책](./media/backup-azure-sql-database/differential-backup-policy.png)

1. **로그 백업** 에 해당 하는 **편집** 링크를 선택 하 여 기본 설정을 수정 합니다.

    * **로그 백업** 에서 **사용** 을 선택한 다음, 빈도 및 보존 컨트롤을 설정합니다.
    * 로그 백업은 최소 15분 간격으로 수행할 수 있으며 최대 35일 동안 보존할 수 있습니다.
    * 데이터베이스가 [단순 복구 모델](/sql/relational-databases/backup-restore/recovery-models-sql-server)에 있는 경우 해당 데이터베이스에 대 한 로그 백업 일정이 일시 중지 되며 로그 백업이 트리거되지 않습니다.
    * 데이터베이스의 복구 모델이 **전체** 에서 **단순** 으로 변경 되는 경우 복구 모델을 변경 하 여 24 시간 이내에 로그 백업이 일시 중지 됩니다. 마찬가지로 복구 모델이 **단순** 에서 변경 되는 경우 데이터베이스에 대 한 모든 로그 백업이 지원 될 수 있습니다. 복구 모델에서 변경 된 24 시간 이내에 로그 백업 일정을 사용할 수 있습니다.

      ![로그 백업 정책](./media/backup-azure-sql-database/log-backup-policy.png)

1. **백업 정책** 메뉴에서 **SQL 백업 압축** 을 사용할지 여부를 선택합니다. 이 옵션은 기본적으로 사용하지 않도록 설정됩니다. 사용하도록 설정하면 SQL Server가 압축된 백업 스트림을 VDI로 보냅니다. Azure Backup은이 컨트롤의 값에 따라 압축/NO_COMPRESSION 절이 있는 인스턴스 수준 기본값을 재정의 합니다.

1. 백업 정책 편집을 완료 한 후, **확인** 을 선택합니다.

> [!NOTE]
> 각 로그 백업은 이전의 전체 백업에 연결되어 복구 체인을 형성합니다. 이 전체 백업은 마지막 로그 백업의 보존 기간이 만료될 때까지 유지됩니다. 따라서 모든 로그가 복구될 수 있도록 전체 백업이 추가 기간 동안 보존될 수 있습니다. 매주 전체 백업, 일별 차등 및 2 시간 로그가 있다고 가정해 보겠습니다. 모든 항목은 30일 동안 유지됩니다. 그러나 매주 전체 백업을 사용할 수 있는 경우, 즉 30 ~ 7 일 후에만 매주 전체를 정리/삭제할 수 있습니다. 예를 들어 매주 전체 백업은 11 월 16 일에 발생 합니다. 보존 정책에 따라이는 Dec 년 12 월까지 유지 되어야 합니다. 이 전체 백업에 대한 마지막 로그 백업은 예약된 다음 전체 백업일(11월 22일) 이전에 실행됩니다. 이 로그는 12월 22일까지 사용할 수 있으며, 11월 16일 전체 백업본은 그때까지 삭제할 수 없습니다. 따라서 11월 16일 전체 백업본은 12월 22일까지 유지됩니다.

## <a name="enable-auto-protection"></a>자동 보호 사용  

자동 보호를 사용하도록 설정하면 모든 기존 데이터베이스와 이후 데이터베이스가 독립 실행형 SQL Server 인스턴스 또는 Always On 가용성 그룹에 자동으로 백업됩니다.

* 한 번에 자동 보호를 위해 선택할 수 있는 데이터베이스 수에는 제한이 없습니다. 검색은 일반적으로 8 시간 마다 실행 됩니다. 그러나 **db** 다시 검색 옵션을 선택 하 여 수동으로 검색을 실행 하는 경우 새 데이터베이스를 즉시 검색 하 고 보호할 수 있습니다.
* 자동 보호를 사용하도록 설정하면 인스턴스에서 선택적으로 데이터베이스를 보호하거나 보호 대상에서 제외할 수 없습니다.
* 인스턴스에 이미 보호된 데이터베이스가 포함되어 있는 경우 해당 데이터베이스는 자동 보호를 설정한 후에도 해당 정책에 따라 보호된 상태로 유지됩니다. 나중에 추가된 모든 보호되지 않은 데이터베이스에는 자동 보호 설정 당시에 정의된 단일 정책만 적용됩니다. 이는 **구성 백업** 에 나열되어 있습니다. 그러나 자동으로 보호되는 데이터베이스와 연결된 정책을 나중에 변경할 수 있습니다.  

자동 보호를 사용하도록 설정하려면 다음을 수행합니다.

  1. **백업할 항목** 에서 자동 보호를 사용하도록 설정하려는 인스턴스를 선택합니다.
  2. **AUTOPROTECT** 에서 드롭다운 목록을 선택하고 **켜기** 를 선택한 후 **확인** 을 선택합니다.

      ![가용성 그룹에 자동 보호를 사용하도록 설정](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 백업은 모든 데이터베이스에 대해 함께 구성되며 **백업 작업** 에서 추적할 수 있습니다.

자동 보호를 사용하지 않도록 설정해야 하는 경우 **백업 구성** 아래에서 인스턴스 이름을 선택한 후 해당 인스턴스에 대해 **자동 보호 사용 안 함** 을 선택합니다. 모든 데이터베이스는 계속 백업되지만 이후의 데이터베이스는 자동으로 보호되지 않습니다.

![해당 인스턴스에서 자동 보호를 사용하지 않도록 설정](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>다음 단계

방법 배우기:

* [백업된 SQL Server 데이터베이스 복원](restore-sql-database-azure-vm.md)
* [백업된 SQL Server 데이터베이스 관리](manage-monitor-sql-database-backup.md)
