---
title: Azure Active Directory 통합을 위한 클러스터 구성
titleSuffix: Azure HDInsight
description: Azure Active Directory Domain Services 및 Enterprise Security Package 기능을 사용 하 여 Active Directory와 통합 된 HDInsight 클러스터를 설정 하 고 구성 하는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020, contperf-fy21q2
ms.date: 10/30/2020
ms.openlocfilehash: 6f478b97464cd47e9d0e04bfe83bd48a2b3bfe7c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867103"
---
# <a name="configure-hdinsight-clusters-for-azure-active-directory-integration-with-enterprise-security-package"></a>Enterprise Security Package와 Azure Active Directory 통합을 위해 HDInsight 클러스터 구성

이 문서에서는 Azure Active Directory와 통합 된 HDInsight 클러스터를 만들고 구성 하는 프로세스에 대 한 요약 및 개요를 제공 합니다. 이 통합은 Enterprise Security Package (ESP), Azure Active Directory Domain Services (Azure AD DS) 및 기존 온-프레미스 Active Directory 이라는 HDInsight 기능을 사용 합니다.

Azure에서 도메인을 설정 및 구성 하 고 ESP 사용 클러스터를 만든 다음 온-프레미스 사용자를 동기화 하는 방법에 대 한 단계별 자습서는 [Azure HDInsight에서 Enterprise Security Package 클러스터 만들기 및 구성](apache-domain-joined-create-configure-enterprise-security-cluster.md)을 참조 하세요.

## <a name="background"></a>배경

Enterprise Security Package (ESP)는 Azure HDInsight에 대 한 Active Directory 통합을 제공 합니다. 이러한 통합을 통해 도메인 사용자는 도메인 자격 증명을 사용 하 여 HDInsight 클러스터를 인증 하 고 빅 데이터 작업을 실행할 수 있습니다.

> [!NOTE]  
> ESP는 Apache Spark, Interactive, Hadoop 및 HBase 클러스터 유형에 대해 일반적으로 HDInsight 3.6 및 4.0에서 사용할 수 있습니다. Apache Kafka 클러스터 형식에 대 한 ESP는 최상의 지원과 함께 미리 보기 상태입니다. ESP GA 날짜 이전에 만든 ESP 클러스터 (2018 년 10 월 1 일)는 지원 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

ESP 사용 HDInsight 클러스터를 만들려면 몇 가지 필수 구성 요소를 완료 해야 합니다.

- 기존 온-프레미스 Active Directory 및 Azure Active Directory.
- Azure AD DS를 사용 하도록 설정 합니다.
- 동기화가 완료 되었는지 확인 하려면 Azure AD DS 상태를 확인 하세요.
- 관리 id를 만들고 권한을 부여 합니다.
- DNS 및 관련 문제에 대 한 네트워킹 설치를 완료 합니다.

이러한 각 항목에 대해서는 아래에서 자세히 설명 합니다. 이러한 단계를 모두 완료 하는 연습은 [Azure HDInsight에서 Enterprise Security Package 클러스터 만들기 및 구성](apache-domain-joined-create-configure-enterprise-security-cluster.md)을 참조 하세요.

### <a name="enable-azure-ad-ds"></a>Azure AD DS 사용

Azure AD DS을 사용 하도록 설정 하는 것은 ESP로 HDInsight 클러스터를 만들기 위한 필수 구성 요소입니다. 자세한 내용은 [Azure Portal를 사용 하 여 Azure Active Directory Domain Services 사용](../../active-directory-domain-services/tutorial-create-instance.md)을 참조 하세요.

Azure AD DS을 사용 하도록 설정 하면 모든 사용자와 개체가 기본적으로 Azure Active Directory (Azure AD)에서 Azure AD DS로 동기화 되기 시작 합니다. 동기화 작업의 길이는 Azure AD의 개체 수에 따라 달라집니다. 수백 개의 개체를 동기화 하는 데 몇 일이 걸릴 수 있습니다.

HDInsight를 사용 하려면 Azure AD DS에서 사용 하는 도메인 이름은 39 자이 하 여야 합니다.

HDInsight 클러스터에 액세스 해야 하는 그룹만 동기화 하도록 선택할 수 있습니다. 특정 그룹만 동기화하는 이 옵션은 *범위가 지정된 동기화* 라고 합니다. 지침은 [AZURE AD에서 관리 되는 도메인으로 범위 동기화 구성](../../active-directory-domain-services/scoped-synchronization.md)을 참조 하세요.

보안 LDAP를 사용 하도록 설정 하는 경우 주체 이름에 도메인 이름을 입력 합니다. 인증서의 주체 대체 이름. 도메인 이름이 *contoso100.onmicrosoft.com* 인 경우 인증서 주체 이름 및 주체 대체 이름에 정확한 이름이 있는지 확인 합니다. 자세한 내용은 [Azure AD DS 관리되는 도메인에 대해 보안 LDAP 구성](../../active-directory-domain-services/tutorial-configure-ldaps.md)을 참조하세요.

다음 예제에서는 자체 서명 된 인증서를 만듭니다. 도메인 이름 *contoso100.onmicrosoft.com* 는 `Subject` (주체 이름) 및 `DnsName` (주체 대체 이름)입니다.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

> [!NOTE]  
> 테 넌 트 관리자만 Azure AD DS를 사용 하도록 설정할 수 있습니다. 클러스터 저장소가 Azure Data Lake Storage Gen1 되거나 Gen2 경우에는 기본 Kerberos 인증을 사용 하 여 클러스터에 액세스 해야 하는 사용자에 대해서만 Azure AD Multi-Factor Authentication를 사용 하지 않도록 설정 해야 합니다.
>
> HDInsight 클러스터의 가상 네트워크에 대 한 IP 범위에 액세스 하는 경우에 *만* [신뢰할 수 있는 Ip](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) 또는 [조건부 액세스](../../active-directory/conditional-access/overview.md) 를 사용 하 여 특정 사용자에 대해 Multi-Factor Authentication를 사용 하지 않도록 설정할 수 있습니다. 조건부 액세스를 사용 하는 경우의 Active Directory 서비스 끝점이 HDInsight 가상 네트워크에서 사용 하도록 설정 되었는지 확인 합니다.
>
> 클러스터 저장소가 Azure Blob storage 인 경우 Multi-Factor Authentication를 사용 하지 않도록 설정 하지 마십시오.

### <a name="check-azure-ad-ds-health-status"></a>Azure AD DS 상태 확인

**관리** 범주에서 **상태** 를 선택 하 여 Azure Active Directory Domain Services의 상태를 확인 합니다. Azure AD DS의 상태가 녹색 (실행 중)이 고 동기화가 완료 되었는지 확인 합니다.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png" alt-text="Azure AD DS 상태" border="true":::

### <a name="create-and-authorize-a-managed-identity"></a>관리 id 만들기 및 권한 부여

*사용자 할당 관리 id* 를 사용 하 여 보안 도메인 서비스 작업을 간소화 합니다. 관리 id에 **HDInsight 도메인 서비스 참가자** 역할을 할당 하면 도메인 서비스 작업을 읽고, 만들고, 수정 하 고, 삭제할 수 있습니다.

HDInsight Enterprise Security Package에 Ou 및 서비스 주체 만들기와 같은 특정 도메인 서비스 작업이 필요 합니다. 모든 구독에서 관리 id를 만들 수 있습니다. 일반적으로 관리 되는 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)를 참조 하세요. Azure HDInsight에서 관리 id가 작동 하는 방식에 대 한 자세한 내용은 [Azure hdinsight에서 관리 되는 id](../hdinsight-managed-identities.md)를 참조 하세요.

ESP 클러스터를 설정 하려면 사용자 할당 관리 id가 아직 없는 경우 새로 만듭니다. [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)을 참조하세요.

그런 다음 Azure AD DS에 대 한 **액세스 제어** 에서 관리 Id에 **HDInsight 도메인 서비스 참가자** 역할을 할당 합니다. 이 역할을 할당 하려면 Azure AD DS 관리자 권한이 있어야 합니다.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png" alt-text="Azure Active Directory Domain Services 액세스 제어" border="true":::

**HDInsight Domain Services 참가자** 역할을 할당 하면이 Id가 `on behalf of` Azure AD DS 도메인에서 도메인 서비스 작업을 수행할 수 있는 적절 한 () 액세스 권한이 보장 됩니다. 이러한 작업에는 Ou 만들기 및 삭제가 포함 됩니다.

관리 id에 역할이 지정 된 후 Azure AD DS 관리자는 해당 id를 사용 하는 사용자를 관리 합니다. 먼저 관리자가 포털에서 관리 id를 선택 합니다. 그런 다음 **개요** 에서 **Access Control (IAM)** 을 선택 합니다. 관리자는 ESP 클러스터를 만들려는 사용자 또는 그룹에 **관리 Id 운영자** 역할을 할당 합니다.

예를 들어 Azure AD DS 관리자는 **sjmsi** 관리 id에 대 한 **MarketingTeam** 그룹에이 역할을 할당할 수 있습니다. 예제가 다음 이미지에 표시됩니다. 이렇게 할당 하면 조직의 올바른 사용자가 관리 되는 id를 사용 하 여 ESP 클러스터를 만들 수 있습니다.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png" alt-text="HDInsight 관리 ID 운영자 역할 할당" border="true":::

### <a name="network-configuration"></a>네트워크 구성

> [!NOTE]  
> Azure AD DS는 Azure Resource Manager 기반 가상 네트워크에 배포 해야 합니다. Azure AD DS에서는 클래식 가상 네트워크가 지원 되지 않습니다. 자세한 내용은 [Azure Portal를 사용 하 여 Azure Active Directory Domain Services 사용](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)을 참조 하세요.

Azure AD DS를 사용하도록 설정합니다. 그런 다음 로컬 DNS (Domain Name System) 서버가 Active Directory Vm (가상 컴퓨터)에서 실행 됩니다. 이러한 사용자 지정 DNS 서버를 사용 하도록 Azure AD DS 가상 네트워크를 구성 합니다. 올바른 IP 주소를 찾으려면 **관리** 범주에서 **속성** 을 선택 하 고 **VIRTUAL NETWORK의 IP 주소** 아래를 확인 합니다.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png" alt-text="로컬 DNS 서버에 대 한 IP 주소 찾기" border="true":::

Azure AD DS 가상 네트워크에서 DNS 서버의 구성을 변경 합니다. 이러한 사용자 지정 Ip를 사용 하려면 **설정** 범주에서 **DNS 서버** 를 선택 합니다. 그런 다음 **사용자 지정** 옵션을 선택 하 고, 텍스트 상자에 첫 번째 IP 주소를 입력 하 고, **저장** 을 선택 합니다. 동일한 단계를 사용 하 여 IP 주소를 더 추가 합니다.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png" alt-text="가상 네트워크 DNS 구성을 업데이트 하는 중" border="true":::

Azure AD DS 인스턴스와 HDInsight 클러스터를 동일한 Azure 가상 네트워크에 배치하는 것이 더 쉽습니다. 다른 가상 네트워크를 사용 하려는 경우 해당 가상 네트워크를 피어 링 하 여 도메인 컨트롤러가 HDInsight Vm에 표시 되도록 해야 합니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

가상 네트워크를 피어 링 한 후 사용자 지정 DNS 서버를 사용 하도록 HDInsight 가상 네트워크를 구성 합니다. DNS 서버 주소를 입력 하 여 Azure AD DS 개인 Ip를 입력 합니다. 두 가상 네트워크에서 동일한 DNS 서버를 사용 하는 경우 사용자 지정 도메인 이름은 올바른 IP로 확인 되 고 HDInsight에서 연결할 수 있습니다. 예를 들어 도메인 이름이 인 경우 `contoso.com` 이 단계를 완료 하면 `ping contoso.com` 올바른 AZURE AD DS IP로 확인 되어야 합니다.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png" alt-text="피어 링 가상 네트워크에 대 한 사용자 지정 DNS 서버 구성" border="true":::

HDInsight 서브넷에 NSG (네트워크 보안 그룹) 규칙을 사용 하는 경우 인바운드 및 아웃 바운드 트래픽 모두에 대해 [필요한 ip](../hdinsight-management-ip-addresses.md) 를 허용 해야 합니다.

네트워크 설정을 테스트 하려면 Windows VM을 HDInsight 가상 네트워크/서브넷에 연결 하 고 도메인 이름을 ping 합니다. (IP로 확인 되어야 합니다.) **ldp.exe** 를 실행 하 여 Azure AD DS 도메인에 액세스 합니다. 그런 다음이 Windows VM을 도메인에 가입 하 여 클라이언트와 서버 간에 필요한 모든 RPC 호출이 성공 했는지 확인 합니다.

**Nslookup** 을 사용 하 여 저장소 계정에 대 한 네트워크 액세스를 확인 합니다. 또는 사용할 수 있는 외부 데이터베이스 (예: 외부 Hive metastore 또는 레인저 DB). NSG가 Azure AD DS를 보호 하는 경우 Azure AD DS 서브넷의 NSG 규칙에서 [필요한 포트가](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) 허용 되는지 확인 합니다. 이 Windows VM의 도메인 가입에 성공 하면 다음 단계를 계속 하 여 ESP 클러스터를 만들 수 있습니다.

## <a name="create-an-hdinsight-cluster-with-esp"></a>ESP를 사용 하 여 HDInsight 클러스터 만들기

이전 단계를 올바르게 설정 하 고 나면 다음 단계는 ESP를 사용 하는 HDInsight 클러스터를 만드는 것입니다. HDInsight 클러스터를 만들 때 **보안 + 네트워킹** 탭에서 Enterprise Security Package를 사용 하도록 설정할 수 있습니다. 배포용 Azure Resource Manager 템플릿은 포털 환경을 한 번 사용 합니다. 그런 다음 나중에 다시 사용할 수 있도록 **검토 + 만들기** 페이지에서 미리 채워진 템플릿을 다운로드 합니다.

클러스터를 만드는 동안 [HDINSIGHT ID Broker](identity-broker.md) 기능을 사용 하도록 설정할 수도 있습니다. ID 브로커 기능을 사용 하면 Multi-Factor Authentication를 사용 하 여 Ambari에 로그인 하 고 Azure AD DS에서 암호 해시가 없어도 필요한 Kerberos 티켓을 가져올 수 있습니다.

> [!NOTE]  
> ESP 클러스터 이름의 처음 6자는 사용자 환경에서 고유해야 합니다. 예를 들어 서로 다른 가상 네트워크에 여러 ESP 클러스터가 있는 경우 클러스터 이름의 처음 6 자가 고유 하도록 하는 명명 규칙을 선택 합니다.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png" alt-text="Azure HDInsight Enterprise Security Package에 대 한 도메인 유효성 검사" border="true":::

ESP를 사용 하도록 설정 하면 Azure AD DS와 관련 된 일반적인 잘못 된 구성이 자동으로 검색 되 고 유효성이 검사 됩니다. 이러한 오류를 해결 한 후에는 다음 단계를 계속 진행할 수 있습니다.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png" alt-text="Azure HDInsight Enterprise Security Package 도메인 유효성 검사에 실패 함" border="true":::

ESP를 사용 하 여 HDInsight 클러스터를 만들 때 다음 매개 변수를 제공 해야 합니다.

* **클러스터 관리 사용자**: 동기화 된 Azure AD DS 인스턴스에서 클러스터에 대 한 관리자를 선택 합니다. 이 도메인 계정은 이미 동기화 되어 Azure AD DS에서 사용할 수 있어야 합니다.

* **클러스터 액세스 그룹**: 동기화 하려는 사용자와 클러스터에 대 한 액세스 권한이 있는 보안 그룹은 Azure AD DS에서 사용할 수 있어야 합니다. 예를 들면 HiveUsers 그룹이 있습니다. 자세한 내용은 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

* **LDAPS URL**: 예를 들면 `ldaps://contoso.com:636` 입니다.

사용자가 만든 관리 되는 id는 새 클러스터를 만들 때 **사용자 할당 관리 id** 드롭다운 목록에서 선택할 수 있습니다.

:::image type="content" source="./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png" alt-text="Azure HDINSIGHT ESP Active Directory Domain Services 관리 id" border="true":::입니다.

## <a name="next-steps"></a>다음 단계

* Hive 정책 구성 및 Hive 쿼리 실행에 대한 내용은 [ESP가 포함된 HDInsight 클러스터에 대한 Apache Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 ESP로 HDInsight 클러스터에 연결하는 방법은 [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Apache Hadoop과 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)을 참조하세요.
