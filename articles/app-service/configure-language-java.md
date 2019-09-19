---
title: Konfigurowanie aplikacji Java dla systemu Windows — Azure App Service | Microsoft Docs
description: Dowiedz się, jak skonfigurować aplikacje Java do uruchamiania w domyślnych wystąpieniach systemu Windows w Azure App Service.
keywords: Azure App Service, Web App, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 91f0c059d22fb921aeb0c65f7d4eba95debd530d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097734"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurowanie aplikacji Java systemu Windows dla Azure App Service

Azure App Service umożliwia deweloperom języka Java szybkie tworzenie, wdrażanie i skalowanie aplikacji sieci Web Tomcat w ramach w pełni zarządzanej usługi opartej na systemie Windows. Wdrażaj aplikacje z wtyczkami Maven z poziomu wiersza polecenia lub w edytorach, takich jak IntelliJ, zaćmienie lub Visual Studio Code.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka Java korzystających z programu w App Service. Jeśli nie korzystasz z Azure App Service, musisz najpierw przeczytać [Przewodnik Szybki Start dla języka Java](app-service-web-get-started-java.md) . Ogólne pytania dotyczące korzystania z App Service, które nie są specyficzne dla programowania w języku Java, są odpowiedzi na [często zadawane pytania dotyczące App Service systemu Windows](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Wdrażanie aplikacji

Aby Azure App Service wdrożyć pliki War, można użyć [wtyczki Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . Wdrożenie ze popularną środowisk IDE jest również obsługiwane w przypadku [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) lub [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

W przeciwnym razie metoda wdrażania będzie zależeć od typu archiwum:

- Aby wdrożyć pliki War do Tomcat, użyj `/api/wardeploy/` punktu końcowego, aby opublikować plik archiwum. Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [tę dokumentację](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).

Nie należy wdrażać. War przy użyciu protokołu FTP. Narzędzie FTP służy do przekazywania skryptów uruchamiania, zależności lub innych plików czasu wykonywania. Nie jest to optymalna opcja wdrażania aplikacji sieci Web.

## <a name="logging-and-debugging-apps"></a>Rejestrowanie i debugowanie aplikacji

Raporty o wydajności, wizualizacje ruchu i narzędzia dla kondycji są dostępne dla każdej aplikacji za pomocą Azure Portal. Aby uzyskać więcej informacji, zobacz [Omówienie diagnostyki Azure App Service](overview-diagnostics.md).

### <a name="ssh-console-access"></a>Dostęp do konsoli SSH

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe dzienników w Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Rejestrowanie aplikacji

Włącz [Rejestrowanie aplikacji](troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enable-application-logging-windows) za pomocą Azure Portal lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/webapp/log#az-webapp-log-config) w celu skonfigurowania App Service zapisywania strumieni błędów konsoli standardowej i standardowego programu na potrzeby aplikacji w lokalnym systemie plików lub BLOB Storage platformy Azure. Rejestrowanie w lokalnym wystąpieniu systemu plików App Service jest wyłączone 12 godzin od jego skonfigurowania. Jeśli potrzebujesz już przechowywania, skonfiguruj aplikację do zapisywania danych wyjściowych do kontenera magazynu obiektów BLOB. Dzienniki aplikacji Java i Tomcat można znaleźć w katalogu */LogFiles/Application/* .

Jeśli aplikacja korzysta z [Logback](https://logback.qos.ch/) lub [Log4J](https://logging.apache.org/log4j) do śledzenia, można przekazać te ślady do przeglądu na platformie Azure Application Insights przy użyciu instrukcji konfiguracyjnych platformy rejestrowania w temacie [Eksplorowanie dzienników śledzenia Java w Application Insights ](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Dostosowywanie i dostrajanie

Azure App Service obsługuje Dostosowywanie i Dostosowywanie pól za pomocą Azure Portal i interfejsu wiersza polecenia. Zapoznaj się z następującymi artykułami dotyczącymi konfiguracji aplikacji sieci Web niezależnej od języka Java:

- [Konfigurowanie ustawień aplikacji](configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Skonfiguruj domenę niestandardową](app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Włącz protokół SSL](app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Dodawanie sieci CDN](../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Skonfiguruj witrynę kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Ustawianie opcji środowiska uruchomieniowego języka Java

Aby ustawić przydzieloną pamięć lub inne opcje środowiska uruchomieniowego JVM, Utwórz [ustawienie aplikacji](configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) o nazwie `JAVA_OPTS` z opcjami. App Service przekazuje to ustawienie jako zmienną środowiskową do środowiska uruchomieniowego Java podczas jego uruchamiania.

W Azure Portal w obszarze **Ustawienia aplikacji** dla aplikacji sieci Web Utwórz nowe ustawienie aplikacji o nazwie `JAVA_OPTS` , które zawiera dodatkowe ustawienia, na przykład. `-Xms512m -Xmx1204m`

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

- Wystąpienia B1 i S1:`-Xms1024m -Xmx1024m`
- Wystąpienia B2 i S2:`-Xms3072m -Xmx3072m`
- Wystąpienia B3 i S3:`-Xms6144m -Xmx6144m`

Podczas dostrajania ustawień sterty aplikacji Przejrzyj szczegóły planu App Service i Uwzględnij wiele aplikacji oraz miejsce wdrożenia, aby znaleźć optymalną alokację pamięci.

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

W Azure Portal w obszarze **Ustawienia aplikacji** dla aplikacji sieci Web Utwórz nowe ustawienie aplikacji o nazwie `JAVA_OPTS` z wartością. `-Dfile.encoding=UTF-8`

Alternatywnie można skonfigurować ustawienie aplikacji przy użyciu wtyczki App Service Maven. Dodaj nazwę ustawienia i Tagi wartości w konfiguracji wtyczki:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>Pliki JSP przed kompilacją

Aby zwiększyć wydajność aplikacji Tomcat, możesz skompilować pliki JSP przed wdrożeniem do App Service. Możesz użyć [wtyczki Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) dostarczonej przez Apache Sling lub przy użyciu tego [pliku kompilacji ANT](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpiecz aplikacje

Aplikacje Java działające w App Service mają ten sam zestaw [najlepszych](/azure/security/security-paas-applications-using-app-services) rozwiązań w zakresie zabezpieczeń, co inne aplikacje.

### <a name="authenticate-users-easy-auth"></a>Uwierzytelnianie użytkowników (łatwa autoryzacja)

Skonfiguruj uwierzytelnianie aplikacji w Azure Portal przy użyciu opcji **uwierzytelnianie i autoryzacja** . W tym miejscu możesz włączyć uwierzytelnianie przy użyciu usług Azure Active Directory lub towarzyskich, takich jak Facebook, Google i GitHub. Konfiguracja Azure Portal działa tylko podczas konfigurowania jednego dostawcy uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie aplikacji App Service do korzystania z Azure Active Directory logowania](configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) i pokrewnych artykułów dla innych dostawców tożsamości. Jeśli musisz włączyć wielu dostawców logowania, postępuj zgodnie z instrukcjami w artykule [dostosowywanie App Service Authentication](app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) .

#### <a name="tomcat-and-wildfly"></a>Tomcat i Wildfly

Aplikacja Tomcat lub Wildfly może uzyskać dostęp do oświadczeń użytkownika bezpośrednio z serwletu przez rzutowanie obiektu podmiotu zabezpieczeń na obiekt mapy. Obiekt mapy będzie mapować każdy typ oświadczenia do kolekcji oświadczeń dla tego typu. W poniższym `request` kodzie jest `HttpServletRequest`wystąpienie.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Teraz można sprawdzić `Map` obiekt pod kątem określonego żądania. Na przykład poniższy fragment kodu iteruje przez wszystkie typy roszczeń i drukuje zawartość każdej kolekcji.

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

Aby wylogować użytkowników, użyj `/.auth/ext/logout` ścieżki. Aby wykonać inne czynności, zapoznaj się z dokumentacją dotyczącą [korzystania z App Service uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Dostępna jest również Oficjalna dokumentacja [interfejsu Tomcat HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) i jego metod. Następujące metody serwletu są również odwodnione na podstawie konfiguracji App Service:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Aby wyłączyć tę funkcję, należy utworzyć ustawienie aplikacji o `WEBSITE_AUTH_SKIP_PRINCIPAL` nazwie z `1`wartością. Aby wyłączyć wszystkie filtry serwletu dodane przez App Service, Utwórz ustawienie o nazwie `WEBSITE_SKIP_FILTERS` z `1`wartością.

### <a name="configure-tlsssl"></a>Konfigurowanie protokołu TLS/SSL

Postępuj zgodnie z instrukcjami podanymi w temacie [Powiązywanie istniejącego niestandardowego certyfikatu SSL](app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) , aby przekazać istniejący certyfikat SSL i powiązać go z nazwą domeny aplikacji. Domyślnie aplikacja będzie nadal zezwalać na połączenia HTTP — wykonaj określone czynności opisane w samouczku, aby wymusić stosowanie protokołów SSL i TLS.

### <a name="use-keyvault-references"></a>Użyj odwołań do magazynu kluczy

[Magazyn kluczy platformy Azure](../key-vault/key-vault-overview.md) oferuje scentralizowane zarządzanie kluczami tajnymi przy użyciu zasad dostępu i historii inspekcji. Wpisy tajne (takie jak hasła lub parametry połączeń) można przechowywać w magazynie kluczy i uzyskiwać do nich dostęp w aplikacjach przy użyciu zmiennych środowiskowych.

Najpierw postępuj zgodnie z instrukcjami dotyczącymi [udzielania dostępu aplikacji do Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) i [dokonywania odwołania do magazynu kluczy w ustawieniach aplikacji](app-service-key-vault-references.md#reference-syntax). Można sprawdzić, czy odwołanie jest rozpoznawane jako wpis tajny, drukując zmienną środowiskową podczas zdalnego uzyskiwania dostępu do terminalu App Service.

Aby wstrzyknąć te wpisy tajne w pliku konfiguracji wiosennej lub Tomcat, użyj składni iniekcji`${MY_ENV_VAR}`zmiennych środowiskowych (). W przypadku plików konfiguracji wiosennej zapoznaj się z tą dokumentacją w temacie [konfiguracje zewnętrzne](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Konfigurowanie platform APM

W tej sekcji przedstawiono sposób łączenia aplikacji Java wdrożonych w systemie Azure App Service w systemie Linux z platformami NewRelic i AppDynamics Application Performance Monitoring (APM).

### <a name="configure-new-relic"></a>Konfiguruj nowe Relic

1. Utwórz nowe konto Relic na [newrelic.com](https://newrelic.com/signup)
2. Pobierz agenta Java z NewRelic, jego nazwa będzie wyglądać podobnie do *newrelic-Java-x. x. x. zip*.
3. Skopiuj klucz licencji, który będzie potrzebny do późniejszego skonfigurowania agenta.
4. Za pomocą [konsoli kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) Utwórz nowy katalog */Home/site/wwwroot/APM*.
5. Przekaż pliki w postaci unpackd New Relic Java Agent do katalogu w obszarze */Home/site/wwwroot/APM*. Pliki dla agenta powinny znajdować się w */Home/site/wwwroot/APM/newrelic*.
6. Zmodyfikuj plik YAML pod adresem */Home/site/wwwroot/APM/newrelic/newrelic.yml* i Zastąp wartość licencji zastępczej własnym kluczem licencji.
7. W Azure Portal przejdź do aplikacji w App Service i Utwórz nowe ustawienie aplikacji.
    - Jeśli aplikacja używa **języka Java SE**, Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` z wartością. `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`
    - Jeśli używasz **Tomcat**, Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` z wartością. `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`

### <a name="configure-appdynamics"></a>Konfigurowanie AppDynamics

1. Utwórz konto AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Pobierz agenta Java z witryny internetowej AppDynamics nazwa pliku będzie podobna do *AppServerAgent-x. x. x. xxxxx. zip*
3. Za pomocą [konsoli kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) Utwórz nowy katalog */Home/site/wwwroot/APM*.
4. Przekaż pliki agenta Java do katalogu w obszarze */Home/site/wwwroot/APM*. Pliki dla agenta powinny znajdować się w */Home/site/wwwroot/APM/AppDynamics*.
5. W Azure Portal przejdź do aplikacji w App Service i Utwórz nowe ustawienie aplikacji.
    - Jeśli używasz **języka Java SE**, Utwórz zmienną środowiskową o nazwie `JAVA_OPTS` przy użyciu wartości `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , `<app-name>` gdzie to nazwa App Service.
    - Jeśli używasz **Tomcat**, Utwórz zmienną środowiskową o nazwie `CATALINA_OPTS` przy użyciu wartości `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , gdzie `<app-name>` to nazwa App Service.

>  Jeśli masz już zmienną środowiskową dla `JAVA_OPTS` lub `CATALINA_OPTS`, Dołącz `-javaagent:/...` opcję do końca bieżącej wartości.

## <a name="data-sources"></a>Źródła danych

### <a name="tomcat"></a>Tomcat

Te instrukcje dotyczą wszystkich połączeń z bazą danych. Musisz wypełnić symbole zastępcze nazwą klasy sterownika wybranej bazy danych i plikiem JAR. Dostarczono tabelę z nazwami klas i pobraniami sterowników dla wspólnych baz danych.

| Database (Baza danych)   | Nazwa klasy sterownika                             | Sterownik JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Pobieranie](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Pobierz](https://dev.mysql.com/downloads/connector/j/) (Wybierz pozycję "Platforma niezależna") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Pobieranie](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Aby skonfigurować Tomcat do korzystania z łączności z bazą danych Java (JDBC) lub interfejsu API trwałości Java (JPA), `CATALINA_OPTS` najpierw Dostosuj zmienną środowiskową, która jest odczytywana przez Tomcat podczas uruchamiania. Ustaw te wartości za pomocą ustawienia aplikacji w [dodatku App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Lub Ustaw zmienne środowiskowe na stronie**Ustawienia aplikacji** **konfiguracji** > w Azure Portal.

Następnie ustal, czy źródło danych powinno być dostępne dla jednej aplikacji, czy dla wszystkich aplikacji uruchomionych na Tomcat serwletu.

#### <a name="application-level-data-sources"></a>Źródła danych na poziomie aplikacji

1. Utwórz plik *Context. XML* w *META-INF/* katalogu projektu. Utwórz *plik META-INF/* katalog, jeśli nie istnieje.

2. W *pliku Context. XML*Dodaj `Context` element, aby połączyć źródło danych z adresem JNDI. Zastąp `driverClassName` symbol zastępczy nazwą klasy sterownika z powyższej tabeli.

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

#### <a name="finalize-configuration"></a>Finalizowanie konfiguracji

Na koniec umieścimy sterownik JARs w ścieżce klasy Tomcat i uruchomisz ponownie App Service. Upewnij się, że pliki sterowników JDBC są dostępne dla Tomcat ClassLoader, umieszczając je w katalogu */Home/Tomcat/lib* . (Utwórz ten katalog, jeśli jeszcze nie istnieje). Aby przekazać te pliki do wystąpienia App Service, wykonaj następujące czynności:

1. W [Cloud Shell](https://shell.azure.com)Zainstaluj rozszerzenie webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Uruchom następujące polecenie interfejsu wiersza polecenia, aby utworzyć tunel SSH z systemu lokalnego do App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Połącz się z lokalnym portem tunelowania przy użyciu klienta SFTP i przekaż pliki do folderu */Home/Tomcat/lib* .

Alternatywnie możesz użyć klienta FTP do przekazania sterownika JDBC. Postępuj zgodnie z tymi [instrukcjami w celu uzyskania poświadczeń FTP](deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="configuring-tomcat"></a>Konfigurowanie Tomcat

Aby edytować Tomcat `server.xml` lub inne pliki konfiguracyjne, najpierw Zanotuj wersję główną Tomcat w portalu.

1. Znajdź katalog macierzysty Tomcat dla swojej wersji, uruchamiając `env` polecenie. Wyszukaj zmienną środowiskową rozpoczynającą się od `AZURE_TOMCAT`i dopasowuje wersję główną. Na przykład `AZURE_TOMCAT85_HOME` wskazuje katalog Tomcat dla Tomcat 8,5.
1. Po zidentyfikowaniu katalogu macierzystego Tomcat dla danej wersji Skopiuj katalog konfiguracji do `D:\home`programu. Na przykład, jeśli `AZURE_TOMCAT85_HOME` ma `D:\Program Files (x86)\apache-tomcat-8.5.37`wartość, Nowa ścieżka `D:\home\apache-tomcat-8.5.37`do skopiowanego katalogu byłaby.

Na koniec uruchom ponownie usługę App Service. Twoje wdrożenia powinny przejść do `D:\home\site\wwwroot\webapps` tak jak wcześniej.

## <a name="java-runtime-statement-of-support"></a>Instrukcja obsługi środowiska uruchomieniowego Java

### <a name="jdk-versions-and-maintenance"></a>JDK wersje i konserwacja

Obsługiwany zestaw Java Development Kit (JDK) platformy Azure jest [Zulu](https://www.azul.com/downloads/azure-only/zulu/) udostępniany przez [systemy Azul](https://www.azul.com/).

Aktualizacje wersji głównej zostaną udostępnione za poorednictwem nowych opcji środowiska uruchomieniowego w Azure App Service dla systemu Windows. Klienci mogą aktualizować te nowsze wersje środowiska Java przez skonfigurowanie wdrożenia App Service i są odpowiedzialne za testowanie i upewnienie się, że główna aktualizacja spełnia ich potrzeby.

Obsługiwane zestawy JDK są automatycznie poprawiane co kwartał w styczniu, kwietniu, lipcu i październiku każdego roku. Aby uzyskać więcej informacji na temat języka Java na platformie Azure, zobacz [ten dokument pomocy technicznej](https://docs.microsoft.com/azure/java/jdk/).

### <a name="security-updates"></a>Aktualizacje zabezpieczeń

Poprawki i poprawki dla głównych luk w zabezpieczeniach zostaną wydane, gdy tylko staną się dostępne w systemach Azul. Usterka "główna" jest definiowana przez podstawowy wynik 9,0 lub wyższy w [systemie oceny typowych luk w zabezpieczeniach NIST, wersja 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Wycofanie i wycofanie

Jeśli obsługiwane środowisko uruchomieniowe języka Java zostanie wycofane, deweloperzy platformy Azure korzystający z środowiska uruchomieniowego, którego to dotyczy, otrzymają powiadomienie o wycofaniu z co najmniej sześciu miesięcy przed wycofaniem środowiska uruchomieniowego.

### <a name="local-development"></a>Programowanie lokalne

Deweloperzy mogą pobrać wersję produkcyjną Azul Zulu Enterprise JDK na potrzeby lokalnego opracowywania z [witryny pobierania programu Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Obsługa programowania

Pomoc techniczna dla platformy [Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) jest dostępna w firmie Microsoft podczas tworzenia aplikacji dla platformy azure lub [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) z [wykwalifikowanym planem pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Obsługa środowiska uruchomieniowego

Deweloperzy mogą [otworzyć problem](/azure/azure-supportability/how-to-create-azure-support-request) z usługą Azul Zulu zestawy JDK za pomocą pomocy technicznej systemu Azure, jeśli ma on [wykwalifikowany plan pomocy technicznej](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Następne kroki

Ten temat zawiera instrukcję obsługi środowiska uruchomieniowego Java dla Azure App Service w systemie Windows.

- Aby dowiedzieć się więcej na temat hostowania aplikacji sieci Web za pomocą Azure App Service zobacz [App Service Omówienie](overview.md).
- Aby uzyskać informacje na temat programowania w języku Java na platformie Azure, zobacz artykuł [Azure for Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
