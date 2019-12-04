---
title: Nawiązywanie połączenia z Azure App Service-Azure Database for MySQL
description: Instrukcje dotyczące prawidłowego łączenia istniejących Azure App Service z Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 63ef9ac55fcfaebfd58ae1ccdb34107d41900be5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770547"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Połącz istniejące Azure App Service z serwerem Azure Database for MySQL
W tym temacie wyjaśniono, jak połączyć istniejące Azure App Service z serwerem Azure Database for MySQL.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Zaloguj się do [portalu Azure](https://portal.azure.com). Utwórz serwer Azure Database for MySQL. Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć serwer Azure Database for MySQL z poziomu portalu](quickstart-create-mysql-server-database-using-azure-portal.md) lub [jak utworzyć serwer Azure Database for MySQL przy użyciu interfejsu wiersza polecenia](quickstart-create-mysql-server-database-using-azure-cli.md).

Obecnie istnieją dwa rozwiązania umożliwiające dostęp z Azure App Service do Azure Database for MySQL. Oba rozwiązania obejmują Konfigurowanie reguł zapory na poziomie serwera.

## <a name="solution-1---allow-azure-services"></a>Rozwiązanie 1 — Zezwalanie na usługi platformy Azure
Azure Database for MySQL zapewnia zabezpieczenia dostępu przy użyciu zapory do ochrony danych. Podczas nawiązywania połączenia z Azure App Service z serwerem Azure Database for MySQL należy pamiętać, że wychodzące adresy IP App Service są dynamiczne. Wybranie opcji "Zezwalaj na dostęp do usług platformy Azure" pozwoli usłudze App Service na łączenie się z serwerem MySQL.

1. W bloku ustawienia serwera MySQL kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć blok zabezpieczenia połączenia dla Azure Database for MySQL.

   ![Azure Portal kliknij pozycję zabezpieczenia połączeń](./media/howto-connect-webapp/1-connection-security.png)

2. Wybierz pozycję **włączone** w obszarze **Zezwalaj na dostęp do usług platformy Azure**, a następnie **Zapisz**.
   ![Azure Portal — Zezwalanie na dostęp do platformy Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Rozwiązanie 2 — Tworzenie reguły zapory w celu jawnego zezwalania na wychodzące adresy IP
Można jawnie dodać wszystkie wychodzące adresy IP Azure App Service.

1. W bloku właściwości App Service Wyświetl **wychodzący adres IP**.

   ![Azure Portal — wyświetlanie wychodzących adresów IP](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. W bloku zabezpieczenia połączenia MySQL Dodaj wychodzące adresy IP jeden według jednego.

   ![Azure Portal — Dodawanie jawnych adresów IP](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Pamiętaj, aby **zapisać** reguły zapory.

Mimo że usługa Azure App Service próbuje zachować stałą adresy IP w czasie, istnieją przypadki, w których mogą się zmieniać adresy IP. Na przykład może się to zdarzyć, gdy następuje odtwarzanie aplikacji lub przeprowadzenie operacji skalowania lub gdy nowe komputery są dodawane w regionalnych centrach danych platformy Azure w celu zwiększenia pojemności. Gdy adresy IP zmienią się, aplikacja może spowodować przestoje w przypadku wystąpienia, w którym nie można już nawiązać połączenia z serwerem MySQL. Należy pamiętać o tym przy wyborze jednego z wcześniejszych rozwiązań.

## <a name="ssl-configuration"></a>Konfiguracja protokołu SSL
Azure Database for MySQL ma domyślnie włączony protokół SSL. Jeśli aplikacja nie korzysta z protokołu SSL w celu nawiązania połączenia z bazą danych, należy wyłączyć protokół SSL na serwerze MySQL. Aby uzyskać szczegółowe informacje na temat konfigurowania protokołu SSL, zobacz [Używanie protokołu SSL z Azure Database for MySQL](howto-configure-ssl.md).

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Parametry połączenia](howto-connection-string.md).
