---
title: Konfigurowanie aplikacji Java dla systemu Linux — Azure App Service | Microsoft Docs
description: Dowiedz się, jak skonfigurować aplikacje Java działające w Azure App Service w systemie Linux.
keywords: Azure App Service, Web App, Linux, OSS, Java, Java EE, JEE, JavaEE
services: app-service
author: bmitchell287
manager: barbkess
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 9625870132d088bf1de6df06f05f0cac41a1e7fa
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144218"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurowanie aplikacji Java dla systemu Linux dla Azure App Service

Azure App Service w systemie Linux umożliwia deweloperom języka Java szybkie tworzenie, wdrażanie i skalowanie aplikacji sieci Web Tomcat lub Java Standard Edition (SE) spakowanych w pełni zarządzanej usługi opartej na systemie Linux. Wdrażaj aplikacje z wtyczkami Maven z poziomu wiersza polecenia lub w edytorach, takich jak IntelliJ, zaćmienie lub Visual Studio Code.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka Java, którzy używają wbudowanego kontenera systemu Linux w App Service. Jeśli nie korzystasz z Azure App Service, najpierw postępuj zgodnie z samouczkiem [przewodnika Szybki Start](quickstart-java.md) i [Java z programem PostgreSQL](tutorial-java-enterprise-postgresql-app.md) .

## <a name="deploying-your-app"></a>Wdrażanie aplikacji

Aby Azure App Service wdrożyć pliki jar i War, można użyć [wtyczki Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . Wdrożenie ze popularną środowisk IDE jest również obsługiwane w przypadku [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) lub [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

W przeciwnym razie metoda wdrażania będzie zależeć od typu archiwum:

- Aby wdrożyć pliki War do Tomcat, użyj punktu końcowego `/api/wardeploy/`, aby OPUBLIKOWAĆ plik archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Aby wdrożyć pliki jar w obrazach środowiska Java SE, użyj punktu końcowego `/api/zipdeploy/` witryny kudu. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nie należy wdrażać. War lub. jar przy użyciu protokołu FTP. Narzędzie FTP służy do przekazywania skryptów uruchamiania, zależności lub innych plików czasu wykonywania. Nie jest to optymalna opcja wdrażania aplikacji sieci Web.

## <a name="logging-and-debugging-apps"></a>Rejestrowanie i debugowanie aplikacji

Raporty o wydajności, wizualizacje ruchu i narzędzia dla kondycji są dostępne dla każdej aplikacji za pomocą Azure Portal. Aby uzyskać więcej informacji, zobacz [Omówienie diagnostyki Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Dostęp do konsoli SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe dzienników w Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Rejestrowanie aplikacji

Włącz [Rejestrowanie aplikacji](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enable-application-logging-windows) za pomocą Azure Portal lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/webapp/log#az-webapp-log-config) w celu skonfigurowania App Service zapisywania strumieni błędów konsoli standardowej i standardowego programu na potrzeby aplikacji w lokalnym systemie plików lub BLOB Storage platformy Azure. Rejestrowanie w lokalnym wystąpieniu systemu plików App Service jest wyłączone 12 godzin od jego skonfigurowania. Jeśli potrzebujesz już przechowywania, skonfiguruj aplikację do zapisywania danych wyjściowych do kontenera magazynu obiektów BLOB. Dzienniki aplikacji Java i Tomcat można znaleźć w katalogu */Home/LogFiles/Application/* .

Jeśli aplikacja korzysta z [Logback](https://logback.qos.ch/) lub [Log4J](https://logging.apache.org/log4j) do śledzenia, można przekazać te ślady do przeglądu na platformie Azure Application Insights przy użyciu instrukcji konfiguracyjnych platformy rejestrowania w temacie [Eksplorowanie dzienników śledzenia Java w Application Insights ](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Narzędzia do rozwiązywania problemów

Wbudowane obrazy języka Java są oparte na systemie operacyjnym [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Użyj Menedżera pakietów `apk`, aby zainstalować narzędzia do rozwiązywania problemów lub polecenia.

### <a name="flight-recorder"></a>Rejestrator lotu

Wszystkie obrazy Java w systemie Linux na App Service mają zainstalowany Rejestrator lotu Zulu, dzięki czemu można łatwo połączyć się z JVM i rozpocząć nagrywanie profilera lub wygenerować zrzut sterty.

#### <a name="timed-recording"></a>Rejestrowanie czasu

Aby rozpocząć, Użyj protokołu SSH do App Service i uruchom polecenie `jcmd`, aby wyświetlić listę wszystkich uruchomionych procesów języka Java. Oprócz samej jcmd powinna zostać wyświetlona aplikacja Java działająca z IDENTYFIKATORem procesu (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Wykonaj poniższe polecenie, aby rozpocząć rejestrowanie 30-sekundowe JVM. Spowoduje to profilowanie JVM i utworzenie pliku JFR o nazwie *jfr_example. JFR* w katalogu macierzystym. (Zastąp 116 identyfikatorem PID aplikacji Java).

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Podczas 30-sekundowego interwału można sprawdzić, czy nagranie odbywa się przez uruchomienie `jcmd 116 JFR.check`. Spowoduje to wyświetlenie wszystkich nagrań dla danego procesu języka Java.

#### <a name="continuous-recording"></a>Ciągłe nagrywanie

Rejestratora lotów Zulu można użyć do ciągłego profilowania aplikacji Java z minimalnym wpływem na wydajność środowiska uruchomieniowego ([Źródło](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Aby to zrobić, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure w celu utworzenia ustawienia aplikacji o nazwie JAVA_OPTS z niezbędną konfiguracją. Zawartość ustawienia aplikacji JAVA_OPTS są przesyłane do polecenia `java`, gdy aplikacja zostanie uruchomiona.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Po rozpoczęciu rejestrowania można zrzucić bieżące dane rejestrowania w dowolnym momencie za pomocą polecenia `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Aby uzyskać więcej informacji, zobacz [Jcmd polecenia](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analizowanie nagrań

Użyj [FTPS](../deploy-ftp.md) , aby pobrać plik JFR na komputer lokalny. Aby przeanalizować plik JFR, Pobierz i zainstaluj [sterowanie misjami Zulu](https://www.azul.com/products/zulu-mission-control/). Aby uzyskać instrukcje na temat sterowania Zulu, zapoznaj się z [dokumentacją Azul](https://docs.azul.com/zmc/) i [instrukcjami dotyczącymi instalacji](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Dostosowywanie i dostrajanie

Azure App Service dla systemu Linux obsługuje Dostosowywanie i Dostosowywanie pól za pomocą Azure Portal i interfejsu wiersza polecenia. Zapoznaj się z następującymi artykułami dotyczącymi konfiguracji aplikacji sieci Web niezależnej od języka Java:

- [Konfigurowanie ustawień aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Skonfiguruj domenę niestandardową](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurowanie powiązań SSL](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Dodawanie sieci CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Skonfiguruj witrynę kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ustawianie opcji środowiska uruchomieniowego języka Java

Aby ustawić przydzieloną pamięć lub inne opcje środowiska uruchomieniowego JVM w środowiskach Tomcat i Java SE, należy utworzyć [ustawienie aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) o nazwie `JAVA_OPTS` z opcjami. App Service Linux przekazuje to ustawienie jako zmienną środowiskową do środowiska uruchomieniowego Java podczas jego uruchamiania.

W Azure Portal w obszarze **Ustawienia aplikacji** dla aplikacji sieci Web Utwórz nowe ustawienie aplikacji o nazwie `JAVA_OPTS`, które zawiera dodatkowe ustawienia, takie jak `-Xms512m -Xmx1204m`.

Aby skonfigurować ustawienie aplikacji z wtyczki Maven, Dodaj Tagi ustawienia/wartość w sekcji wtyczka platformy Azure. W poniższym przykładzie ustawiono określony minimalny i maksymalny rozmiar sterty Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Deweloperzy korzystający z jednej aplikacji z jednym miejscem wdrożenia w ramach planu App Service mogą korzystać z następujących opcji:

- Wystąpienia B1 i S1: `-Xms1024m -Xmx1024m`
- Wystąpienia B2 i S2: `-Xms3072m -Xmx3072m`
- Wystąpienia B3 i S3: `-Xms6144m -Xmx6144m`

Podczas dostrajania ustawień sterty aplikacji Przejrzyj szczegóły planu App Service i Uwzględnij wiele aplikacji oraz miejsce wdrożenia, aby znaleźć optymalną alokację pamięci.

Jeśli wdrażasz aplikację JAR, powinna ona mieć nazwę *App. jar* , aby wbudowany obraz mógł prawidłowo identyfikować aplikację. (Wtyczka Maven zmienia nazwę automatycznie). Jeśli nie chcesz zmienić nazwy pliku JAR na *App. jar*, możesz przekazać skrypt powłoki przy użyciu polecenia, aby uruchomić plik JAR. Następnie wklej pełną ścieżkę do tego skryptu w polu tekstowym [plik startowy](app-service-linux-faq.md#built-in-images) w sekcji konfiguracja portalu. Skrypt uruchamiania nie jest uruchamiany z katalogu, w którym został umieszczony. W związku z tym zawsze należy używać ścieżek bezwzględnych do odwoływania się do plików w skrypcie uruchomieniowym (na przykład: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Włącz gniazda sieci Web

Włącz obsługę gniazd sieci Web w Azure Portal w **ustawieniach aplikacji** dla aplikacji. Aby ustawienie zaczęło obowiązywać, należy ponownie uruchomić aplikację.

Włącz obsługę funkcji gniazdo sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure z następującym poleceniem:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Następnie uruchom ponownie aplikację:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ustaw domyślne kodowanie znaków

W Azure Portal w obszarze **Ustawienia aplikacji** dla aplikacji sieci Web Utwórz nowe ustawienie aplikacji o nazwie `JAVA_OPTS` z wartością `-Dfile.encoding=UTF-8`.

Alternatywnie można skonfigurować ustawienie aplikacji przy użyciu wtyczki App Service Maven. Dodaj nazwę ustawienia i Tagi wartości w konfiguracji wtyczki:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Dopasuj limit czasu uruchamiania

Jeśli aplikacja Java jest szczególnie duża, należy zwiększyć limit czasu uruchamiania. W tym celu należy utworzyć ustawienie aplikacji `WEBSITES_CONTAINER_START_TIME_LIMIT` i ustawić dla niego liczbę sekund, które App Service powinny upłynąć przed upływem limitu czasu. Wartość maksymalna to `1800` sekund.

### <a name="pre-compile-jsp-files"></a>Pliki JSP przed kompilacją

Aby zwiększyć wydajność aplikacji Tomcat, możesz skompilować pliki JSP przed wdrożeniem do App Service. Możesz użyć [wtyczki Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) dostarczonej przez Apache Sling lub przy użyciu tego [pliku kompilacji ANT](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpieczanie aplikacji

Aplikacje Java działające w App Service dla systemu Linux mają taki sam zestaw najlepszych rozwiązań w zakresie [zabezpieczeń](/azure/security/security-paas-applications-using-app-services) jak inne aplikacje.

### <a name="authenticate-users-easy-auth"></a>Uwierzytelnianie użytkowników (łatwa autoryzacja)

Skonfiguruj uwierzytelnianie aplikacji w Azure Portal przy użyciu opcji **uwierzytelnianie i autoryzacja** . W tym miejscu możesz włączyć uwierzytelnianie przy użyciu usług Azure Active Directory lub towarzyskich, takich jak Facebook, Google i GitHub. Konfiguracja Azure Portal działa tylko podczas konfigurowania jednego dostawcy uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie aplikacji App Service do korzystania z Azure Active Directory logowania](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) i pokrewnych artykułów dla innych dostawców tożsamości. Jeśli musisz włączyć wielu dostawców logowania, postępuj zgodnie z instrukcjami w artykule [dostosowywanie App Service Authentication](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) .

#### <a name="tomcat-and-wildfly"></a>Tomcat i Wildfly

Aplikacja Tomcat lub Wildfly może uzyskać dostęp do oświadczeń użytkownika bezpośrednio z serwletu przez rzutowanie obiektu podmiotu zabezpieczeń na obiekt mapy. Obiekt mapy będzie mapować każdy typ oświadczenia do kolekcji oświadczeń dla tego typu. W poniższym kodzie `request` jest wystąpieniem `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Teraz można sprawdzić, czy `Map` obiekt dla każdego określonego żądania. Na przykład poniższy fragment kodu iteruje przez wszystkie typy roszczeń i drukuje zawartość każdej kolekcji.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Aby wylogować użytkowników, użyj ścieżki `/.auth/ext/logout`. Aby wykonać inne czynności, zapoznaj się z dokumentacją dotyczącą [korzystania z App Service uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Dostępna jest również Oficjalna dokumentacja [interfejsu Tomcat HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) i jego metod. Następujące metody serwletu są również odwodnione na podstawie konfiguracji App Service:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Aby wyłączyć tę funkcję, należy utworzyć ustawienie aplikacji o nazwie `WEBSITE_AUTH_SKIP_PRINCIPAL` z wartością `1`. Aby wyłączyć wszystkie filtry serwletu dodane przez App Service, Utwórz ustawienie o nazwie `WEBSITE_SKIP_FILTERS` o wartości `1`.

#### <a name="spring-boot"></a>Spring Boot

Deweloperzy rozruchu sprężynowego mogą korzystać z [Azure Active Directory sprężynowego rozruchu Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) do zabezpieczania aplikacji przy użyciu dobrze znanych adnotacji i interfejsów API zabezpieczeń. Pamiętaj, aby zwiększyć maksymalny rozmiar nagłówka w pliku *Application. Properties* . Sugerujemy wartość `16384`.

### <a name="configure-tlsssl"></a>Konfigurowanie protokołu TLS/SSL

Postępuj zgodnie z instrukcjami w polu [Zabezpiecz niestandardową nazwę DNS z powiązaniem SSL w Azure App Service](../configure-ssl-bindings.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) , aby przekazać istniejący certyfikat SSL i powiązać go z nazwą domeny aplikacji. Domyślnie aplikacja będzie nadal zezwalać na połączenia HTTP — wykonaj określone czynności opisane w samouczku, aby wymusić stosowanie protokołów SSL i TLS.

### <a name="use-keyvault-references"></a>Użyj odwołań do magazynu kluczy

[Magazyn kluczy platformy Azure](../../key-vault/key-vault-overview.md) oferuje scentralizowane zarządzanie kluczami tajnymi przy użyciu zasad dostępu i historii inspekcji. Wpisy tajne (takie jak hasła lub parametry połączeń) można przechowywać w magazynie kluczy i uzyskiwać do nich dostęp w aplikacjach przy użyciu zmiennych środowiskowych.

Najpierw postępuj zgodnie z instrukcjami dotyczącymi [udzielania dostępu aplikacji do Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) i [dokonywania odwołania do magazynu kluczy w ustawieniach aplikacji](../app-service-key-vault-references.md#reference-syntax). Można sprawdzić, czy odwołanie jest rozpoznawane jako wpis tajny, drukując zmienną środowiskową podczas zdalnego uzyskiwania dostępu do terminalu App Service.

Aby wstrzyknąć te wpisy tajne w pliku konfiguracji wiosennej lub Tomcat, użyj składni iniekcji zmiennych środowiskowych (`${MY_ENV_VAR}`). W przypadku plików konfiguracji wiosennej zapoznaj się z tą dokumentacją w temacie [konfiguracje zewnętrzne](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Korzystanie z magazynu kluczy języka Java

Domyślnie wszystkie certyfikaty publiczne lub prywatne [przekazane do App Service Linux](../configure-ssl-certificate.md) zostaną załadowane do magazynu kluczy Java w trakcie uruchamiania kontenera. Oznacza to, że przekazane certyfikaty będą dostępne w kontekście połączenia podczas tworzenia wychodzących połączeń TLS. Po przekazaniu certyfikatu należy ponownie uruchomić App Service, aby można go było załadować do magazynu kluczy Java.

Możesz korzystać z narzędzia klucza Java lub debugować je, [otwierając połączenie SSH](app-service-linux-ssh-support.md) do App Service i uruchamiając polecenie `keytool`. Listę poleceń można znaleźć w dokumentacji dotyczącej [najważniejszych narzędzi](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) . Certyfikaty są przechowywane w domyślnej lokalizacji pliku magazynu języka Java, `$JAVA_HOME/jre/lib/security/cacerts`.

Aby można było zaszyfrować połączenie JDBC, może być wymagana dodatkowa konfiguracja. Zapoznaj się z dokumentacją wybranego sterownika JDBC.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)

#### <a name="manually-initialize-and-load-the-key-store"></a>Ręczne inicjowanie i ładowanie magazynu kluczy

Możesz zainicjować Magazyn kluczy i ręcznie dodać certyfikaty. Utwórz ustawienie aplikacji `SKIP_JAVA_KEYSTORE_LOAD`z wartością `1`, aby wyłączyć App Service ładowania certyfikatów do magazynu kluczy automatycznie. Wszystkie certyfikaty publiczne przekazane do App Service za pośrednictwem witryny Azure Portal są przechowywane w obszarze `/var/ssl/certs/`. Certyfikaty prywatne są przechowywane w obszarze `/var/ssl/private/`.

Aby uzyskać więcej informacji na temat interfejsu API magazynu kluczy, zapoznaj się z [oficjalną dokumentacją](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Konfigurowanie platform APM

W tej sekcji przedstawiono sposób łączenia aplikacji Java wdrożonych w systemie Azure App Service w systemie Linux z platformami NewRelic i AppDynamics Application Performance Monitoring (APM).

### <a name="configure-new-relic"></a>Konfiguruj nowe Relic

1. Utwórz konto NewRelic na [newrelic.com](https://newrelic.com/signup)
2. Pobierz agenta Java z NewRelic, jego nazwa będzie wyglądać podobnie do *newrelic-Java-x. x. x. zip*.
3. Skopiuj klucz licencji, który będzie potrzebny do późniejszego skonfigurowania agenta.
4. Użyj protokołu [SSH do wystąpienia App Service](app-service-linux-ssh-support.md) i Utwórz nowy katalog */Home/site/wwwroot/APM*.
5. Przekaż pliki agenta NewRelic języka Java rozpakowane do katalogu w obszarze */Home/site/wwwroot/APM*. Pliki dla agenta powinny znajdować się w */Home/site/wwwroot/APM/newrelic*.
6. Zmodyfikuj plik YAML pod adresem */Home/site/wwwroot/APM/newrelic/newrelic.yml* i Zastąp wartość licencji zastępczej własnym kluczem licencji.
7. W Azure Portal przejdź do aplikacji w App Service i Utwórz nowe ustawienie aplikacji.
    - Jeśli aplikacja korzysta z **języka Java SE**, Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` z `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`wartością.
    - Jeśli używasz **Tomcat**, Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` przy użyciu `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`wartości.
    - Jeśli używasz programu **WildFly**, zobacz nową dokumentację Relic w [tym miejscu](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) , aby uzyskać wskazówki dotyczące instalowania agenta Java i konfiguracji JBoss.

### <a name="configure-appdynamics"></a>Konfigurowanie AppDynamics

1. Utwórz konto AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Pobierz agenta Java z witryny internetowej AppDynamics nazwa pliku będzie podobna do *AppServerAgent-x. x. x. xxxxx. zip*
3. Użyj protokołu [SSH do wystąpienia App Service](app-service-linux-ssh-support.md) i Utwórz nowy katalog */Home/site/wwwroot/APM*.
4. Przekaż pliki agenta Java do katalogu w obszarze */Home/site/wwwroot/APM*. Pliki dla agenta powinny znajdować się w */Home/site/wwwroot/APM/AppDynamics*.
5. W Azure Portal przejdź do aplikacji w App Service i Utwórz nowe ustawienie aplikacji.
    - Jeśli używasz **języka Java SE**, Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` z wartością `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, gdzie `<app-name>` jest nazwą App Service.
    - Jeśli używasz **Tomcat**, Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` z wartością `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, gdzie `<app-name>` jest nazwą App Service.
    - Jeśli używasz **WildFly**, zapoznaj się z dokumentacją AppDynamics w celu uzyskania [wskazówek dotyczących instalowania](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) agenta Java i konfiguracji JBoss.

>  Jeśli masz już zmienną środowiskową dla `JAVA_OPTS` lub `CATALINA_OPTS`, Dołącz opcję `-javaagent:/...` do końca bieżącej wartości.

## <a name="configure-jar-applications"></a>Konfigurowanie aplikacji JAR

### <a name="starting-jar-apps"></a>Uruchamianie aplikacji JAR

Domyślnie App Service oczekuje, że aplikacja JAR ma nazwę *App. jar*. Jeśli ma tę nazwę, zostanie ona uruchomiona automatycznie. W przypadku użytkowników Maven można ustawić nazwę JAR, dołączając `<finalName>app</finalName>` w sekcji `<build>` *pliku pliku pom. XML*. [Można to zrobić w Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) , ustawiając właściwość `archiveFileName`.

Jeśli chcesz użyć innej nazwy dla systemu JAR, musisz również podać [polecenie uruchamiania](app-service-linux-faq.md#built-in-images) , które wykonuje plik JAR. Na przykład `java -jar my-jar-app.jar`. Możesz ustawić wartość dla polecenia startowego w portalu, w obszarze Konfiguracja > Ustawienia ogólne lub przy użyciu ustawienia aplikacji o nazwie `STARTUP_COMMAND`.

### <a name="server-port"></a>Port serwera

App Service system Linux kieruje żądania przychodzące do portu 80, dlatego aplikacja powinna również nasłuchiwać na porcie 80. Można to zrobić w konfiguracji aplikacji (np. w pliku *Application. Properties* ) lub w poleceniu uruchamiania (na przykład `java -jar spring-app.jar --server.port=80`). Zapoznaj się z następującą dokumentacją dla typowych platform języka Java:

- [Rozruch sprężynowy](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Platforma odtwarzania](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Źródła danych

### <a name="tomcat"></a>Tomcat

Te instrukcje dotyczą wszystkich połączeń z bazą danych. Musisz wypełnić symbole zastępcze nazwą klasy sterownika wybranej bazy danych i plikiem JAR. Dostarczono tabelę z nazwami klas i pobraniami sterowników dla wspólnych baz danych.

| Database (Baza danych)   | Nazwa klasy sterownika                             | Sterownik JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Pobieranie](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Pobierz](https://dev.mysql.com/downloads/connector/j/) (wybierz pozycję "Platforma niezależna") |
| Oprogramowanie SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Pobieranie](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Aby skonfigurować Tomcat do korzystania z łączności z bazą danych Java (JDBC) lub interfejsu API trwałości Java (JPA), najpierw Dostosuj zmienną środowiskową `CATALINA_OPTS`, która jest odczytywana przez Tomcat w momencie uruchomienia. Ustaw te wartości za pomocą ustawienia aplikacji w [dodatku App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Lub Ustaw zmienne środowiskowe na stronie **konfiguracji** > **ustawienia aplikacji** w Azure Portal.

Następnie ustal, czy źródło danych powinno być dostępne dla jednej aplikacji, czy dla wszystkich aplikacji uruchomionych na Tomcat serwletu.

#### <a name="application-level-data-sources"></a>Źródła danych na poziomie aplikacji

1. Utwórz plik *Context. XML* w *META-INF/* katalogu projektu. Utwórz *plik META-INF/* katalog, jeśli nie istnieje.

2. W *pliku Context. XML*dodaj element `Context`, aby połączyć źródło danych z adresem JNDI. Zastąp symbol zastępczy `driverClassName` nazwą klasy sterownika z powyższej tabeli.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Zaktualizuj *plik Web. XML* aplikacji w taki sposób, aby korzystał ze źródła danych w aplikacji.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Udostępnione zasoby na poziomie serwera

Dodanie udostępnionego źródła danych na poziomie serwera wymaga edytowania pliku tomcat. XML. Najpierw przekaż [skrypt uruchamiania](app-service-linux-faq.md#built-in-images) i Ustaw ścieżkę do skryptu w programie **Configuration** > **Startup**. Można przekazać skrypt uruchamiania przy użyciu [protokołu FTP](../deploy-ftp.md).

Skrypt uruchamiania wykona [przekształcenie XSL](https://www.w3schools.com/xml/xsl_intro.asp) do pliku Server. XML i wyprowadza wynikowy plik xml do `/usr/local/tomcat/conf/server.xml`. Skrypt uruchamiania powinien instalować libxslt za pośrednictwem APK. Plik XSL i skrypt uruchamiania można przekazać za pośrednictwem protokołu FTP. Poniżej znajduje się przykładowy skrypt uruchamiania.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /usr/local/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /home/tomcat/conf/server.xml
```

Poniżej przedstawiono przykładowy plik XSL. Przykładowy plik XSL dodaje nowy węzeł łącznika do tomcat Server. XML.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Na koniec Umieść sterownik JARs w ścieżce klasy Tomcat i ponownie uruchom App Service.

1. Upewnij się, że pliki sterowników JDBC są dostępne dla Tomcat ClassLoader, umieszczając je w katalogu */Home/Tomcat/lib* . (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do wystąpienia App Service, wykonaj następujące czynności:

    1. W [Cloud Shell](https://shell.azure.com)Zainstaluj rozszerzenie webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Połącz się z lokalnym portem tunelowania przy użyciu klienta SFTP i przekaż pliki do folderu */Home/Tomcat/lib* .

    Alternatywnie możesz użyć klienta FTP do przekazania sterownika JDBC. Postępuj zgodnie z tymi [instrukcjami w celu uzyskania poświadczeń FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Jeśli utworzono źródło danych na poziomie serwera, uruchom ponownie aplikację App Service Linux. Program Tomcat resetuje `CATALINA_BASE`, aby `/home/tomcat` i używał zaktualizowanej konfiguracji.

### <a name="spring-boot"></a>Spring Boot

Aby nawiązać połączenie ze źródłami danych w aplikacjach do rozruchu sprężynowego, zalecamy utworzenie parametrów połączenia i dodanie ich do pliku *Application. Properties* .

1. W sekcji "Konfiguracja" na stronie App Service Ustaw nazwę ciągu, wklej parametry połączenia JDBC w polu wartość i ustaw typ na "niestandardowy". Opcjonalnie możesz ustawić te parametry połączenia jako ustawienie gniazda.

    Te parametry połączenia są dostępne dla naszej aplikacji jako zmienna środowiskowa o nazwie `CUSTOMCONNSTR_<your-string-name>`. Na przykład utworzone powyżej parametry połączenia mają nazwę `CUSTOMCONNSTR_exampledb`.

2. W pliku *Application. Properties* odwołując się do tych parametrów połączenia z nazwą zmiennej środowiskowej. W naszym przykładzie będziemy używać następujących sposobów.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Aby uzyskać więcej informacji na temat tego tematu, zobacz [dokumentację dotyczącą rozruchu sprężynowego na stronie dostęp do danych](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) i [konfiguracje zewnętrzne](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="configure-java-ee-wildfly"></a>Konfigurowanie języka Java EE (WildFly)

> [!NOTE]
> Środowisko Java Enterprise Edition w systemie App Service Linux jest obecnie dostępne w wersji zapoznawczej. Ten stos **nie** jest zalecany w przypadku pracy w środowisku produkcyjnym.

Azure App Service w systemie Linux umożliwia deweloperom języka Java kompilowanie, wdrażanie i skalowanie aplikacji Java Enterprise (Java EE) w w pełni zarządzanej usłudze opartej na systemie Linux.  Bazowe środowisko uruchomieniowe środowiska Java Enterprise jest serwerem aplikacji [WildFly](https://wildfly.org/) Open Source.

Ta sekcja zawiera następujące podsekcje:

- [Skalowanie za pomocą App Service](#scale-with-app-service)
- [Dostosuj konfigurację serwera aplikacji](#customize-application-server-configuration)
- [Instalowanie modułów i zależności](#install-modules-and-dependencies)
- [Konfigurowanie źródeł danych](#configure-data-sources)
- [Włącz dostawców obsługi komunikatów](#enable-messaging-providers)

### <a name="scale-with-app-service"></a>Skalowanie za pomocą App Service

Serwer aplikacji WildFly uruchomiony w App Service w systemie Linux działa w trybie autonomicznym, a nie w konfiguracji domeny. Po skalowaniu w poziomie planu App Service każde wystąpienie WildFly jest skonfigurowane jako serwer autonomiczny.

Skalowanie aplikacji w pionie lub poziomie przy użyciu [reguł skalowania](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) i [zwiększenie liczby wystąpień](../manage-scale-up.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Dostosuj konfigurację serwera aplikacji

Wystąpienia aplikacji sieci Web są bezstanowe, dlatego każde uruchomione nowe wystąpienie musi być skonfigurowane podczas uruchamiania, aby obsługiwało konfigurację WildFly wymaganą przez aplikację.
Można napisać uruchamianie skryptu bash w celu wywołania interfejsu wiersza polecenia WildFly w celu:

- Konfigurowanie źródeł danych
- Konfigurowanie dostawców obsługi komunikatów
- Dodaj inne moduły i zależności do konfiguracji serwera WildFly.

Skrypt jest uruchamiany, gdy WildFly jest uruchomiona, ale przed uruchomieniem aplikacji. Skrypt powinien używać [interfejsu wiersza polecenia JBoss](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) wywoływanego z */opt/JBoss/wildfly/bin/JBoss-CLI.sh* , aby skonfigurować serwer aplikacji z dowolną konfiguracją lub zmianami, które są wymagane po uruchomieniu serwera.

Nie należy używać trybu interaktywnego interfejsu wiersza polecenia w celu skonfigurowania WildFly. Zamiast tego można dostarczyć skrypt poleceń do interfejsu wiersza polecenia JBoss za pomocą polecenia `--file`, na przykład:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Za pomocą protokołu FTP Przekaż skrypt uruchamiania do lokalizacji w wystąpieniu App Service w katalogu */Home* , na przykład */Home/site/Deployments/Tools*. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji do Azure App Service przy użyciu protokołu FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Ustaw wartość pola **skrypt uruchamiania** w Azure Portal na lokalizację skryptu powłoki startowej, na przykład */Home/site/Deployments/Tools/Your-Startup-Script.sh*.

Określ [Ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) w konfiguracji aplikacji, aby przekazać zmienne środowiskowe do użycia w skrypcie. Ustawienia aplikacji przechowują parametry połączenia i inne wpisy tajne, które są konieczne do skonfigurowania aplikacji poza kontrolą wersji.

### <a name="install-modules-and-dependencies"></a>Instalowanie modułów i zależności

Aby zainstalować moduły i ich zależności w ścieżce klas WildFly za pośrednictwem interfejsu wiersza polecenia JBoss, należy utworzyć następujące pliki w ich własnym katalogu. Niektóre moduły i zależności mogą potrzebować dodatkowej konfiguracji, takiej jak nazwa JNDI lub inna Konfiguracja specyficzna dla interfejsu API, dlatego ta lista jest minimalnym zestawem czynności, które należy skonfigurować zależność w większości przypadków.

- [Deskryptor modułu XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Ten plik XML definiuje nazwę, atrybuty i zależności modułu. Ten [przykładowy plik module. XML](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definiuje moduł Postgres, jego zależność JDBC pliku JAR oraz inne zależności modułu wymagane.
- Wszelkie niezbędne zależności pliku JAR dla Twojego modułu.
- Skrypt z poleceń interfejsu wiersza polecenia JBoss w celu skonfigurowania nowego modułu. Ten plik będzie zawierać polecenia, które zostaną wykonane przez interfejs wiersza polecenia JBoss, aby skonfigurować serwer do korzystania z zależności. Aby uzyskać dokumentację dotyczącą poleceń dodawania modułów, źródeł danych i dostawców obsługi komunikatów, zapoznaj się z [tym dokumentem](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Bash skrypt uruchamiania do wywołania interfejsu wiersza polecenia JBoss i wykonywania skryptu w poprzednim kroku. Ten plik zostanie wykonany, gdy wystąpienie App Service zostanie ponownie uruchomione lub gdy nowe wystąpienia zostaną wdrożone podczas skalowania w poziomie. Ten skrypt uruchamiania pozwala na wykonywanie innych konfiguracji aplikacji w miarę jak polecenia JBoss są przesyłane do interfejsu wiersza polecenia JBoss. Co więcej, ten plik może być pojedynczym poleceniem, aby przekazać skrypt poleceń interfejsu wiersza polecenia JBoss do interfejsu CLI JBoss:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Po umieszczeniu plików i zawartości modułu wykonaj poniższe kroki, aby dodać moduł do serwera aplikacji WildFly.

1. Za pomocą protokołu FTP Przekaż pliki do lokalizacji w wystąpieniu App Service w katalogu */Home* , na przykład */Home/site/Deployments/Tools*. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji do Azure App Service przy użyciu protokołu FTP/S](../deploy-ftp.md).
2. Na stronie **Ustawienia ogólne** > **konfiguracji** Azure Portal Ustaw pole **skrypt uruchamiania** na lokalizację skryptu powłoki startowej, na przykład */Home/site/Deployments/Tools/Startup.sh*.
3. Uruchom ponownie wystąpienie App Service, naciskając przycisk **ponownego uruchomienia** w sekcji **Przegląd** w portalu lub korzystając z interfejsu wiersza polecenia platformy Azure.

### <a name="configure-data-sources"></a>Konfigurowanie źródeł danych

Aby skonfigurować WildFly/JBoss w celu uzyskania dostępu do źródła danych, należy użyć ogólnego procesu przedstawionego powyżej w sekcji "Instalowanie modułów i zależności". W poniższej sekcji znajdują się szczegółowe informacje dotyczące tego procesu dla źródeł danych PostgreSQL, MySQL i SQL Server.

W tej sekcji założono, że masz już aplikację, wystąpienie App Service i wystąpienie usługi Azure Database. Poniższe instrukcje odnoszą się do nazwy App Service, jej grupy zasobów i informacji o połączeniu z bazą danych. Te informacje można znaleźć na Azure Portal.

Jeśli wolisz przejść przez cały proces od początku przy użyciu przykładowej aplikacji, zobacz [Samouczek: Tworzenie aplikacji sieci Web Java EE i Postgres na platformie Azure](tutorial-java-enterprise-postgresql-app.md).

Poniższe kroki wyjaśniają wymagania dotyczące łączenia istniejących App Service i bazy danych.

1. Pobierz sterownik JDBC dla [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)lub [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Rozpakuj pobrane archiwum, aby pobrać plik JAR sterownika.

2. Utwórz plik o nazwie takiej jak *module. XML* i Dodaj następujący znacznik. Zastąp `<module name>` symbol zastępczy (łącznie z nawiasami ostrymi) z `org.postgres` dla PostgreSQL, `com.mysql` dla programu MySQL lub `com.microsoft` dla SQL Server. Zastąp `<JDBC .jar file path>` nazwą pliku JAR z poprzedniego kroku, włącznie z pełną ścieżką do lokalizacji, w której zostanie umieszczony plik w wystąpieniu App Service. Może to być dowolna lokalizacja w katalogu */Home* .

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Utwórz plik o nazwie takiej jak *DataSource-Commands. CLI* i Dodaj następujący kod. Zastąp `<JDBC .jar file path>` wartością użytą w poprzednim kroku. Zastąp `<module file path>` nazwą pliku i ścieżką App Service z poprzedniego kroku, na przykład */Home/module.XML*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Ten plik jest uruchamiany przez skrypt uruchomieniowy opisany w następnym kroku. Instaluje sterownik JDBC jako moduł WildFly, tworzy odpowiednie źródło danych WildFly i ponownie ładuje serwer, aby upewnić się, że zmiany zaczną obowiązywać.

4. Utwórz plik o nazwie takiej jak *Startup.sh* i Dodaj następujący kod. Zastąp `<JBoss CLI script>` nazwą pliku utworzonego w poprzednim kroku. Pamiętaj, aby uwzględnić pełną ścieżkę do lokalizacji, w której zostanie umieszczony plik w wystąpieniu App Service, na przykład */Home/DataSource-Commands.CLI*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Za pomocą protokołu FTP Przekaż plik JDBC. jar, plik XML modułu, skrypt interfejsu wiersza polecenia JBoss i skrypt uruchamiania do wystąpienia App Service. Umieść te pliki w lokalizacji określonej w poprzednich krokach, na przykład */Home*. Aby uzyskać więcej informacji na temat protokołu FTP, zobacz [wdrażanie aplikacji do Azure App Service przy użyciu protokołu FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Użyj interfejsu wiersza polecenia platformy Azure, aby dodać ustawienia do App Service, w którym przechowywane są informacje o połączeniu z bazą danych. Zastąp `<resource group>` i `<webapp name>` wartościami używanymi przez App Service. Zastąp `<database server name>`, `<database name>`, `<admin name>`i `<admin password>` informacjami o połączeniu z bazą danych. Możesz uzyskać informacje o App Service i bazie danych z Azure Portal.

    **PostgreSQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    Wartości DATABASE_CONNECTION_URL są różne dla każdego serwera bazy danych i różnią się od wartości w Azure Portal. Formaty adresów URL pokazane tutaj (i w fragmentach kodu powyżej) są wymagane do użycia przez WildFly:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. W Azure Portal przejdź do App Service i Znajdź stronę **Ustawienia ogólne** > **Konfiguracja** . W polu **skrypt uruchamiania** Ustaw nazwę i lokalizację skryptu uruchomieniowego, na przykład */Home/Startup.sh*.

Przy następnym ponownym uruchomieniu App Service zostanie uruchomiony skrypt uruchamiania i zostaną wykonane niezbędne czynności konfiguracyjne. Aby sprawdzić, czy ta konfiguracja działa prawidłowo, możesz uzyskać dostęp do App Service przy użyciu protokołu SSH, a następnie uruchomić skrypt uruchamiania samodzielnie z poziomu wiersza polecenia bash. Możesz również przejrzeć dzienniki App Service. Aby uzyskać więcej informacji na temat tych opcji, zobacz [Rejestrowanie i debugowanie aplikacji](#logging-and-debugging-apps).

Następnie należy zaktualizować konfigurację WildFly dla aplikacji i wdrożyć ją ponownie. Wykonaj następujące czynności:

1. Otwórz plik *src/Main/sources/META-INF/trwałość. XML* dla aplikacji i znajdź element `<jta-data-source>`. Zastąp jego zawartość, jak pokazano poniżej:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Ponownie skompiluj i Wdróż aplikację przy użyciu następującego polecenia w wierszu bash:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Uruchom ponownie wystąpienie App Service, naciskając przycisk **ponownego uruchomienia** w sekcji **Omówienie** Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure.

Twoje wystąpienie App Service jest teraz skonfigurowane do uzyskiwania dostępu do bazy danych.

Aby uzyskać więcej informacji na temat konfigurowania łączności z bazą danych za pomocą WildFly, zobacz [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)lub [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="enable-messaging-providers"></a>Włącz dostawców obsługi komunikatów

Aby włączyć fasolę opartą na komunikatach przy użyciu Service Bus jako mechanizmu obsługi komunikatów:

1. Użyj [biblioteki Apache QPID JMS Messaging](https://qpid.apache.org/proton/index.html). Uwzględnij tę zależność w pliku pom. XML (lub innym pliku kompilacji) dla aplikacji.

2. Tworzenie [zasobów Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Utwórz Azure Service Bus przestrzeń nazw i kolejkę w tej przestrzeni nazw oraz zasady dostępu współdzielonego z możliwościami wysyłania i odbierania.

3. Przekaż klucz zasad dostępu współdzielonego do kodu przy użyciu adresu URL, aby zakodować klucz podstawowy zasad lub [użyć zestawu SDK Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Wykonaj kroki opisane w sekcji Instalowanie modułów i zależności z deskryptorem XML modułu, zależnościami jar, JBoss interfejsu wiersza polecenia i skryptem uruchamiania dla dostawcy JMS. Oprócz czterech plików należy również utworzyć plik XML, który definiuje nazwę JNDI dla kolejki JMS i tematu. Zobacz [to repozytorium](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) dla plików konfiguracji referencyjnej.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Używanie Redis jako pamięci podręcznej sesji z Tomcat

Można skonfigurować Tomcat do korzystania z zewnętrznego magazynu sesji, takiego jak [pamięć podręczna platformy Azure dla Redis](/azure/azure-cache-for-redis/). Dzięki temu można zachować stan sesji użytkownika (na przykład dane koszyka zakupów), gdy użytkownik zostanie przetransferowany do innego wystąpienia aplikacji, na przykład w przypadku automatycznego skalowania, ponownego uruchomienia lub przejścia w tryb failover.

Aby użyć Tomcat z Redis, musisz skonfigurować aplikację tak, aby korzystała z implementacji [trwałego](http://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) elementumanager. Poniższe kroki wyjaśniają ten proces za pomocą [Menedżera sesji Pivot: Redis — magazyn](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) jako przykład.

1. Otwórz Terminal bash i użyj `export <variable>=<value>`, aby ustawić każdą z następujących zmiennych środowiskowych.

    | Zmienna                 | Wartość                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Nazwa grupy zasobów zawierającej wystąpienie App Service.       |
    | WEBAPP_NAME              | Nazwa wystąpienia App Service.                                     |
    | WEBAPP_PLAN_NAME         | Nazwa planu App Service                                          |
    | REGION                   | Nazwa regionu, w którym jest hostowana aplikacja.                           |
    | REDIS_CACHE_NAME         | Nazwa wystąpienia usługi Azure cache for Redis.                           |
    | REDIS_PORT               | Port SSL, na którym nasłuchuje pamięć podręczna Redis.                             |
    | REDIS_PASSWORD           | Podstawowy klucz dostępu dla danego wystąpienia.                                  |
    | REDIS_SESSION_KEY_PREFIX | Wartość, którą określisz, aby identyfikować klucze sesji, które pochodzą z aplikacji. |

    Informacje o nazwie, porcie i kluczu dostępu do Azure Portal można znaleźć w sekcji **Właściwości** lub **klucze dostępu** swojego wystąpienia usługi.

2. Utwórz lub zaktualizuj plik *src/Main/webapp/META-INF/Context. XML* aplikacji z następującą zawartością:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Ten plik określa i konfiguruje implementację Menedżera sesji dla aplikacji. Używa zmiennych środowiskowych ustawionych w poprzednim kroku, aby zachować informacje o koncie z plików źródłowych.

3. Za pomocą protokołu FTP Przekaż plik JAR Menedżera sesji do wystąpienia App Service, umieszczając je w katalogu */Home/Tomcat/lib* . Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji do Azure App Service przy użyciu protokołu FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Wyłącz [plik cookie koligacji sesji](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) dla wystąpienia App Service. Można to zrobić w Azure Portal, przechodząc **do aplikacji**, a następnie ustawiając **Ustawienia ogólne > konfiguracji > koligacja ARR** . Alternatywnie można użyć następującego polecenia:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Domyślnie App Service będzie używać plików cookie koligacji sesji w celu zapewnienia, że żądania klientów z istniejącymi sesjami są kierowane do tego samego wystąpienia aplikacji. To zachowanie domyślne nie wymaga konfiguracji, ale nie może zachować stanu sesji użytkownika, gdy wystąpienie aplikacji zostanie ponownie uruchomione lub gdy ruch jest przekierowywany do innego wystąpienia. [Wyłączenie istniejącej konfiguracji koligacji wystąpienia ARR](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) w celu wyłączenia routingu opartego na plikach cookie sesji pozwala na działanie skonfigurowanego magazynu sesji bez ingerencji.

5. Przejdź do sekcji **Właściwości** wystąpienia App Service i Znajdź **dodatkowe wychodzące adresy IP**. Reprezentują wszystkie możliwe wychodzące adresy IP dla aplikacji. Skopiuj je do użycia w następnym kroku.

6. Dla każdego adresu IP Utwórz regułę zapory w usłudze Azure cache for Redis. Można to zrobić na Azure Portal z sekcji **Zapora** wystąpienia usługi Redis. Podaj unikatową nazwę każdej reguły, a następnie ustaw wartość **początkowy adres IP** i **końcowy adres IP** na ten sam adres IP.

7. Przejdź do sekcji **Ustawienia zaawansowane** wystąpienia Redis i ustaw opcję **Zezwalaj na dostęp tylko za pośrednictwem protokołu SSL** na wartość **nie**. Dzięki temu wystąpienie App Service może komunikować się z pamięcią podręczną Redis za pośrednictwem infrastruktury platformy Azure.

8. Zaktualizuj konfigurację `azure-webapp-maven-plugin` w pliku *pliku pom. XML* aplikacji, aby odwołać się do informacji o koncie Redis. Ten plik korzysta ze zmiennych środowiskowych ustawionych wcześniej w celu zachowania informacji o koncie z plików źródłowych.

    W razie potrzeby zmień `1.7.0` na bieżącą wersję [wtyczki Maven dla Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Ponownie skompiluj i Wdróż aplikację.

    ```bash
    mvn package
    mvn azure-webapp:deploy
    ```

Twoja aplikacja będzie teraz korzystać z pamięci podręcznej Redis na potrzeby zarządzania sesją.

Aby zapoznać się z przykładem, którego można użyć do przetestowania tych instrukcji, zobacz repozytorium [skalowanie-stanowe-Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) w serwisie GitHub.

## <a name="docker-containers"></a>Kontenerów Docker

Aby skorzystać z platformy Azure Zulu JDK w kontenerach, upewnij się, że pobierasz i korzystasz ze wstępnie utworzonych obrazów zgodnie z opisem z [obsługiwanej strony pobierania Azul Zulu Enterprise for Azure](https://www.azul.com/downloads/azure-only/zulu/) lub skorzystaj z `Dockerfile` przykładów z [repozytorium GitHub firmy Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Zestawienie pomocy technicznej

### <a name="runtime-availability"></a>Dostępność środowiska uruchomieniowego

App Service dla systemu Linux obsługuje dwa środowiska uruchomieniowe do zarządzanej obsługi aplikacji sieci Web Java:

- [Kontener Tomcat serwletu](https://tomcat.apache.org/) do uruchamiania aplikacji spakowanych jako pliki archiwum sieci Web (War). Obsługiwane są wersje 8,5 i 9,0.
- Środowisko uruchomieniowe Java SE do uruchamiania aplikacji spakowanych jako pliki archiwum Java (JAR). Obsługiwane wersje to Java 8 i 11.

### <a name="jdk-versions-and-maintenance"></a>JDK wersje i konserwacja

Azul Zulu Enterprise builds of OpenJDK to bezpłatna, wieloplatformowa, przygotowana do produkcji dystrybucja OpenJDK dla platformy Azure i Azure Stackna przez systemy Microsoft i Azul. Zawierają one wszystkie składniki do kompilowania i uruchamiania aplikacji Java SE. Można zainstalować JDK z [instalacji języka Java JDK](https://aka.ms/azure-jdks).

Obsługiwane zestawy JDK są automatycznie poprawiane co kwartał w styczniu, kwietniu, lipcu i październiku każdego roku.

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Poprawki i poprawki dla głównych luk w zabezpieczeniach zostaną wydane, gdy tylko staną się dostępne w systemach Azul. Usterka "główna" jest definiowana przez podstawowy wynik 9,0 lub wyższy w [systemie oceny typowych luk w zabezpieczeniach NIST, wersja 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Wycofanie i wycofanie

Jeśli obsługiwane środowisko uruchomieniowe języka Java zostanie wycofane, deweloperzy platformy Azure korzystający z środowiska uruchomieniowego, którego to dotyczy, otrzymają powiadomienie o wycofaniu z co najmniej sześciu miesięcy przed wycofaniem środowiska uruchomieniowego.

## <a name="next-steps"></a>Następne kroki

Odwiedź centrum [deweloperów platformy Azure dla języka Java](/java/azure/) , aby znaleźć Przewodniki Szybki Start, samouczki i dokumentację języka Java dla platformy Azure.

Ogólne pytania dotyczące korzystania z App Service dla systemu Linux, które nie są specyficzne dla programowania w języku Java, są odpowiedzi na [często zadawane pytania dotyczące systemu App Service Linux](app-service-linux-faq.md).
