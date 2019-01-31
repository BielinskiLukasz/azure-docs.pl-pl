---
title: Architektura usługi Azure HDInsight z pakietem Enterprise Security
description: Dowiedz się, jak planowanie zabezpieczeń HDInsight z pakietem Enterprise Security.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3e58c22048c9b71b00cffb0657fc924277304662
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462436"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Użyj pakiet Enterprise Security w HDInsight

Klaster w warstwie standardowa usługi Azure HDInsight to klaster z jednego użytkownika. Jest ona odpowiednia dla większości firm, które mają mniejsze zespoły aplikacji tworzące obciążenia dużych ilości danych. Każdego użytkownika można tworzyć dedykowanych klastrów na żądanie i zniszcz go, gdy nie jest już potrzebne. 

Wiele przedsiębiorstw zostały przeniesione na model, w którym klastry są zarządzane przez zespoły IT, a wiele zespołów aplikacji klastrów udziału. Te większych przedsiębiorstw potrzebują wielu użytkowników dostępu do poszczególnych klastrów w usłudze Azure HDInsight.

HDInsight zależy od dostawcy popularnych tożsamości — usłudze Active Directory — w zarządzany sposób. Po zintegrowaniu HDInsight przy użyciu [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), dostęp do tych klastrów przy użyciu poświadczeń domeny. 

Maszyny wirtualne (VM) w HDInsight są przyłączony do domeny podana. Tak wszystkie usługi działające na HDInsight (Apache Ambari, serwerze Apache Hive, Apache Ranger, Apache Spark thrift server i inne) współpracują bezproblemowo dla tego uwierzytelnionego użytkownika. Administratorzy mogą tworzyć zasady silnych autoryzacji przy użyciu struktury Apache Ranger w celu zapewnienia kontroli dostępu opartej na rolach dla zasobów w klastrze.

## <a name="integrate-hdinsight-with-active-directory"></a>Integracja usługi HDInsight z usługą Active Directory

Open source Apache Hadoop opiera się na protokołu Kerberos do uwierzytelniania i zabezpieczeń. W związku z tym węzły klastra HDInsight przy użyciu pakietu zabezpieczeń przedsiębiorstwa (ESP) są przyłączone do domeny, który jest zarządzany przez usługi Azure AD DS. Zabezpieczenia protokołu Kerberos został skonfigurowany do składniki platformy Hadoop w klastrze. 

Następujące elementy są tworzone automatycznie:
- jednostki usługi dla poszczególnych składników usługi Hadoop 
- podmiot zabezpieczeń maszyny dla każdej maszyny, który jest przyłączony do domeny
- Jednostki organizacyjnej (OU) dla każdego klastra do przechowywania podmiotów zabezpieczeń tych usług oraz maszyn 

Aby podsumować, należy skonfigurować środowisko przy użyciu:

- Domeny usługi Active Directory (zarządzane przez usługi Azure AD DS).
- Bezpiecznego protokołu LDAP (LDAPS) włączone w usłudze Azure AD DS.
- Odpowiednie sieci łączność z siecią wirtualną HDInsight, do usługi Azure AD DS sieci wirtualnej, jeśli wybierzesz oddzielne sieci wirtualnej dla nich. Maszynę wirtualną w sieci wirtualnej HDInsight mają bezpośredni kontakt z usługą Azure AD DS za pomocą komunikacji równorzędnej sieci wirtualnej. HDInsight i Azure AD DS w przypadku wdrożenia w tej samej sieci wirtualnej, połączenie jest dostarczana automatycznie i nie trzeba wykonywać dalszych akcji.

## <a name="set-up-different-domain-controllers"></a>Konfigurowanie różnych kontrolerach domeny
HDInsight aktualnie obsługuje tylko usługi Azure AD DS jako kontrolera domeny głównej, używanego przez klaster komunikacji protokołu Kerberos. Jednak inne złożonych konfiguracji usługi Active Directory jest to możliwe, tak długo, jak takiej konfiguracji prowadzi do włączania usługi Azure AD DS dla dostępu HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Usługi Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) zapewnia domeny zarządzanej, która jest w pełni zgodna z usługą Active Directory systemu Windows Server. Microsoft zajmuje się zarządzaniem, obsługą jej poprawek oraz monitorowanie domeny w konfiguracji o wysokiej dostępności (HA). Możesz wdrożyć klaster bez martwienia się o utrzymania kontrolerów domeny. 

Użytkownikom, grupom i hasła są synchronizowane z usługą Azure Active Directory (Azure AD). Jednokierunkowa synchronizacji z wystąpienia usługi Azure AD do usługi Azure AD DS pozwala użytkownikom logować się do klastra przy użyciu tych samych poświadczeń firmowych. 

Aby uzyskać więcej informacji, zobacz [HDInsight konfigurowanie klastrów przy użyciu ESP przy użyciu usługi Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Lokalne usługi Active Directory lub usługi Active Directory na maszynach wirtualnych IaaS

Jeśli masz wystąpienie usługi Active Directory w środowisku lokalnym lub bardziej złożonych konfiguracji usługi Active Directory dla domeny, można zsynchronizować te tożsamości do usługi Azure AD za pomocą usługi Azure AD Connect. Następnie można włączyć usługi Azure AD DS w tej dzierżawie usługi Active Directory. 

Ponieważ protokół Kerberos opiera się na wartości skrótów haseł, należy najpierw [Włączanie synchronizacji skrótów haseł w usłudze Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). 

Jeśli używasz federacji z Active Directory Federation Services (ADFS), konieczne jest włączenie synchronizacji skrótów haseł (zalecane instalacji, zobacz [to](https://youtu.be/qQruArbu2Ew)) który pomaga również odzyskiwania po awarii na wypadek awarii infrastruktury usług AD FS i ochrony z ujawnionymi poświadczeniami. Aby uzyskać więcej informacji, zobacz [Włączanie synchronizacji skrótów haseł za pomocą usługi Azure AD Connect sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Używanie w lokalnej usłudze Active Directory lub usługi Active Directory na maszynach wirtualnych IaaS samodzielnie, bez usługi Azure AD i Azure AD DS, nie jest obsługiwana konfiguracja w przypadku klastrów HDInsight przy użyciu ESP.

Jeśli federacyjnej jest używana i skróty haseł są zsynchronizowane correcty, ale pojawiają się błędy uwierzytelniania,. Sprawdź, czy środowisko powershell jednostki usługi w chmurze uwierzytelniania hasła jest włączona, jeśli nie, należy ustawić [Home obszaru odnajdywania (HRD ) zasad](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) dla Twojej dzierżawy usługi AAD. Do sprawdzenia i zestaw zasad HRD:

 1. Zainstaluj moduł programu powershell usługi Azure AD

 ```
  Install-Module AzureAD
 ```

 2. ```Connect-AzureAD``` przy użyciu poświadczeń administratora globalnego (administratora dzierżawy)

 3. Sprawdź, jeśli została już utworzona nazwa główna usługi "Microsoft Azure Powershell"

```
 $powershellSPN = Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
```

 4. Jeśli nie istnieje (np. w przypadku ($powershellSPN - q $null)) następnie utworzyć nazwę główną usługi

```
 $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
```

 5. Utwórz i Dołącz zasady do tej jednostki usługi: 

```
 $policy = New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuth -Type HomeRealmDiscoveryPolicy

 Add-AzureADServicePrincipalPolicy -Id $powershellSPN.ObjectId -refObjectID $policy.ID
```

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie klastrów HDInsight przy użyciu ESP](apache-domain-joined-configure-using-azure-adds.md)
* [Konfigurowanie zasad usługi Apache Hive dla klastrów HDInsight przy użyciu ESP](apache-domain-joined-run-hive.md)
* [Zarządzanie klastrami HDInsight przy użyciu ESP](apache-domain-joined-manage.md) 
