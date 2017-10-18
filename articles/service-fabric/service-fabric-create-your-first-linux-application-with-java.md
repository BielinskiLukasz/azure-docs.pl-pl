---
title: "Tworzenie aplikacji Java z interfejsem Reliable Actors usługi Azure Service Fabric w systemie Linux | Microsoft Docs"
description: "Dowiedz się, jak utworzyć i wdrożyć aplikację Java z elementami Reliable Actors usługi Service Fabric w ciągu pięciu minut."
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: ryanwi
ms.openlocfilehash: c7625a5670aca5d105601432fedfd0d7a78bb53c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Tworzenie pierwszej aplikacji Java z interfejsem Reliable Actors usługi Service Fabric w systemie Linux
> [!div class="op_single_selector"]
> * [C# — Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java — Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# — Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Ten przewodnik Szybki start pomaga w ciągu kilku minut utworzyć pierwszą aplikację Java usługi Azure Service Fabric w środowisku projektowym w systemie Linux.  W wyniku pracy z przewodnikiem zostanie utworzona prosta jednousługowa aplikacja Java uruchamiana w lokalnym klastrze projektowym.  

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem zainstaluj zestaw SDK usługi Service Fabric oraz interfejs wiersza polecenia usługi Service Fabric i skonfiguruj klaster projektowy w [środowisku projektowym w systemie Linux](service-fabric-get-started-linux.md). Jeśli używasz systemu Mac OS X, możesz [skonfigurować środowisko deweloperskie systemu Linux na maszynie wirtualnej za pomocą narzędzia Vagrant](service-fabric-get-started-mac.md).

Zainstaluj także [interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Instalowanie i konfigurowanie generatorów dla języka Java
Usługa Service Fabric udostępnia narzędzia do tworzenia szkieletu, które ułatwiają tworzenie aplikacji Java usługi Service Fabric z poziomu terminalu przy użyciu generatora szablonów Yeoman. Wykonaj poniższe kroki, aby upewnić się, że generator szablonów Yeoman usługi Service Fabric dla języka Java działa na Twojej maszynie.
1. Zainstaluj środowisko NodeJs i menedżera NPM na swojej maszynie

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Zainstaluj generator szablonów [narzędzia Yeoman](http://yeoman.io/) na swoim komputerze z poziomu narzędzia NPM

  ```bash
  sudo npm install -g yo
  ```
3. Zainstaluj generator aplikacji Java Yeoman usługi Service Fabric z poziomu menedżera NPM

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Podstawowe pojęcia
Aby rozpocząć pracę z elementami Reliable Actors, musisz tylko poznać kilka podstawowych pojęć:

* **Usługa aktora**. Elementy Reliable Actors są spakowane w usługach Reliable Services, które można wdrożyć w infrastrukturze usługi Service Fabric. Wystąpienia aktora są aktywowane w nazwanym wystąpieniu usługi.
* **Rejestracja aktora**. Podobnie jak w przypadku usług Reliable Services, usługę Reliable Actor należy zarejestrować w środowisku uruchomieniowym usługi Service Fabric. Ponadto typ aktora należy zarejestrować w środowisku uruchomieniowym aktora.
* **Interfejs aktora**. Interfejs aktora służy do definiowania jednoznacznie określonego interfejsu publicznego aktora. W terminologii modelu usługi Reliable Actor interfejs aktora definiuje typy komunikatów, które może zrozumieć i przetworzyć aktor. Interfejs aktora jest używany przez innych aktorów i aplikacje klienckie do „wysyłania” (asynchronicznie) komunikatów do aktora. W usługi Reliable Actors można zaimplementować wiele interfejsów.
* **Klasa ActorProxy**. Klasa ActorProxy jest używana przez aplikacje klienckie do wywołania metod ujawnianych za pośrednictwem interfejsu aktora. Klasa ActorProxy udostępnia dwie ważne funkcje:
  
  * Rozpoznawanie nazw: jest w stanie zlokalizować aktora w klastrze (znaleźć węzeł klastra, w którym jest on hostowany).
  * Obsługa błędów: może ponawiać próby wywołania metody i ponownie rozpoznawać lokalizację aktora, na przykład po wystąpieniu błędu, który wymaga przeniesienia aktora do innego węzła w klastrze.

Warto wspomnieć o następujących regułach odnoszących się do interfejsów aktora:

* Metod interfejsu aktora nie można przeciążyć.
* Metody interfejsu aktora nie mogą zawierać parametrów out, ref ani parametrów opcjonalnych.
* Interfejsy ogólne nie są obsługiwane.

## <a name="create-the-application"></a>Tworzenie aplikacji
Aplikacja usługi Service Fabric zawiera jedną lub więcej usług, a każda z nich pełni określoną rolę w dostarczaniu funkcjonalności aplikacji. Generator, który został zainstalowany w ostatniej sekcji, ułatwia utworzenie pierwszej usługi i dodawanie kolejnych w przyszłości.  Możesz też tworzyć, kompilować i wdrażać aplikacje Java usługi Service Fabric przy użyciu wtyczki dla środowiska Eclipse. Zobacz [Create and deploy your first Java application using Eclipse](service-fabric-get-started-eclipse.md) (Tworzenie i wdrażanie pierwszej aplikacji Java za pomocą środowiska Eclipse). Na potrzeby tego przewodnika Szybki start użyj narzędzia Yeoman, aby utworzyć aplikację z jedną usługą, w której jest przechowywana i pobierana wartość licznika.

1. Na terminalu wpisz ``yo azuresfjava``.
2. Nadaj nazwę aplikacji.
3. Wybierz typ pierwszej usługi i nadaj jej nazwę. Na potrzeby tego samouczka wybierz usługę Reliable Actor Service. Aby uzyskać więcej informacji o pozostałych typach usług, zobacz [Service Fabric programming model overview](service-fabric-choose-framework.md) (Omówienie modelu programowania usługi Service Fabric).
   ![Generator Yeoman usługi Service Fabric dla platformy Java][sf-yeoman]

Jeśli aplikacja otrzyma nazwę „HelloWorldActorApplication”, a aktor — „HelloWorldActor”, zostanie utworzony następujący szkielet:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Podstawowe bloki konstrukcyjne usługi Reliable Actors
Podstawowe pojęcia opisane powyżej można przełożyć na podstawowe bloki konstrukcyjne usługi Reliable Actor.

### <a name="actor-interface"></a>Interfejs aktora
Zawiera definicję interfejsu aktora. Ten interfejs definiuje kontrakt aktora udostępniany implementacji aktora i klientom wywołującym aktora, dlatego zazwyczaj warto zdefiniować go w miejscu, które jest oddzielone od implementacji aktora i może zostać udostępnione wielu innym usługom lub aplikacjom klienckim.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Usługa aktora
Zawiera implementację aktora i kod rejestracji aktora. Klasa aktora implementuje interfejs aktora. Jest to miejsce, gdzie aktor wykonuje swoją pracę.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Rejestracja aktora
Usługę aktora należy zarejestrować za pomocą typu usługi w środowisku uruchomieniowym usługi Service Fabric. Aby usługa aktora uruchamiała wystąpienia aktora, w usłudze aktora należy również zarejestrować typ aktora. Metoda rejestracji `ActorRuntime` wykonuje tę pracę dla aktorów.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Kompilowanie aplikacji
Szablony generatora Yeoman usługi Service Fabric obejmują skrypt kompilacji dla narzędzia [Gradle](https://gradle.org/), którego można użyć do skompilowania aplikacji z poziomu terminalu.
Zależności aplikacji Java usługi Service Fabric są pobierane z narzędzia Maven. Aby kompilować aplikacje Java usługi Service Fabric i pracować nad nimi, upewnij się, że zainstalowano zestaw JDK i narzędzie Gradle. Jeśli ich jeszcze nie zainstalowano, możesz uruchomić następujące polecenia, aby zainstalować zestaw JDK (openjdk-8-jdk) i narzędzie Gradle:

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Aby skompilować aplikację i utworzyć jej pakiet, uruchom następujące polecenie:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Wdrażanie aplikacji
Skompilowaną aplikację można wdrożyć w klastrze lokalnym.

1. Połącz się z lokalnym klastrem usługi Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Uruchom skrypt instalacji udostępniony w szablonie, aby skopiować pakiet aplikacji do magazynu obrazów klastra, zarejestrować typ aplikacji i utworzyć wystąpienie aplikacji.

    ```bash
    ./install.sh
    ```

Wdrażanie skompilowanej aplikacji przebiega tak samo jak w przypadku innych aplikacji usługi Service Fabric. Szczegółowe instrukcje są dostępne w dokumentacji dotyczącej [zarządzania aplikacją usługi Service Fabric za pomocą interfejsu wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md).

Parametry tych poleceń można znaleźć w manifestach wygenerowanych w pakiecie aplikacji.

Po wdrożeniu aplikacji otwórz przeglądarkę i przejdź do narzędzia [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) pod adresem [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Następnie rozwiń węzeł **Aplikacje** i zwróć uwagę, że istnieje teraz wpis dla danego typu aplikacji i inny wpis dla pierwszego wystąpienia tego typu.

## <a name="start-the-test-client-and-perform-a-failover"></a>Uruchamianie klienta testowego i przechodzenie w tryb failover
Aktorzy nie pełnią samodzielnie żadnej funkcji. Wymagają wysyłania do nich komunikatów przez inną usługę lub innego klienta. Szablon aktora zawiera prosty skrypt testowy, którego można użyć do interakcji z usługą aktora.

1. Uruchom skrypt za pomocą narzędzia kontrolnego, aby wyświetlić dane wyjściowe usługi aktora.  Skrypt testowy wywołuje metodę `setCountAsync()` dla aktora w celu zwiększenia wartości licznika, wywołuje metodę `getCountAsync()` dla aktora w celu pobrania nowej wartości licznika i wyświetla tę wartość w konsoli.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. W narzędziu Service Fabric Explorer zlokalizuj węzeł, w którym znajduje się replika podstawowa usługi aktora. Na poniższym zrzucie ekranu jest to węzeł 3. Replika podstawowa usługi obsługuje operacje odczytu i zapisu.  Zmiany stanu usługi są następnie replikowane w replikach pomocniczych — na poniższym zrzucie ekranu są one uruchomione w węzłach 0 i 1.

    ![Znajdowanie repliki podstawowej w narzędziu Service Fabric Explorer][sfx-primary]

3. W lokalizacji **Węzły** kliknij węzeł znaleziony w poprzednim kroku, a następnie wybierz pozycję **Dezaktywuj (uruchom ponownie)** z menu Akcje. Ta czynność spowoduje ponowne uruchomienie węzła w replice podstawowej usługi i wymuszenie przejścia w tryb failover do jednej z replik pomocniczych uruchomionych w innym węźle.  Poziom tej repliki pomocniczej zostanie podwyższony do repliki podstawowej, inna replika pomocnicza zostanie utworzona w innym węźle oraz replika podstawowa zacznie obsługiwać operacje odczytu i zapisu. Podczas ponownego uruchamiania węzła należy zwrócić uwagę na dane wyjściowe z klienta testowego oraz to, że licznik będzie nadal się zwiększać niezależnie od trybu failover.

## <a name="remove-the-application"></a>Usuwanie aplikacji
Użyj skryptu odinstalowania udostępnionego w szablonie, aby usunąć wystąpienie aplikacji, wyrejestrować pakiet aplikacji i usunąć pakiet aplikacji z magazynu obrazów klastra.

```bash
./uninstall.sh
```

W narzędziu Service Fabric Explorer aplikacja i typ aplikacji nie są już wyświetlane w węźle **Aplikacje**.

## <a name="service-fabric-java-libraries-on-maven"></a>Biblioteki Java usługi Service Fabric w narzędziu Maven
Biblioteki Java usługi Service Fabric są teraz hostowane w narzędziu Maven. Zależności możesz dodać w pliku ``pom.xml`` lub ``build.gradle`` projektów, aby używać bibliotek Java usługi Service Fabric z repozytorium **mavenCentral**. 

### <a name="actors"></a>Aktorzy

Obsługa interfejsu Reliable Actors usługi Service Fabric dla Twojej aplikacji.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>Usługi

Obsługa interfejsu Reliable Services usługi Service Fabric w Twojej aplikacji.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>Inne
#### <a name="transport"></a>Transport

Obsługa warstwy transportowej dla aplikacji Java usługi Service Fabric. Nie trzeba jawnie dodawać tej zależności do aplikacji Reliable Actors lub aplikacji usługi, chyba że programujesz w warstwie transportowej.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Obsługa usługi Service Fabric

Obsługa na poziomie systemu dla usługi Service Fabric, która komunikuje się z natywnym środowiskiem uruchomieniowym usługi Service Fabric. Nie trzeba jawnie dodawać tej zależności do aplikacji Reliable Actors lub aplikacji usługi. Jest ona automatycznie pobierana z narzędzia Maven, kiedy uwzględnisz pozostałe powyższe zależności.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrowanie starych aplikacji Java usługi Service Fabric na potrzeby użycia z narzędziem Maven
Ostatnio przenieśliśmy biblioteki Java usługi Service Fabric z zestawu SDK Java usługi Service Fabric do repozytorium narzędzia Maven. Nowe aplikacje generowane za pomocą narzędzia Yeoman lub środowiska Eclipse będą generować najnowsze, zaktualizowane projekty (mogące działać z narzędziem Maven), ale możesz zaktualizować swoje istniejące bezstanowe aplikacje Java lub aplikacje Java aktora usługi Service Fabric, które wcześniej korzystały z zestawu SDK Java usługi Service Fabric, aby używały zależności języka Java usługi Service Fabric z narzędzia Maven. Wykonaj kroki wymienione [tutaj](service-fabric-migrate-old-javaapp-to-use-maven.md), aby zapewnić działanie Twojej starszej aplikacji z narzędziem Maven.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie pierwszej aplikacji Java usługi Service Fabric w systemie Linux przy użyciu środowiska Eclipse](service-fabric-get-started-eclipse.md)
* [Dowiedz się więcej o usłudze Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interact with Service Fabric clusters using the Service Fabric CLI (Interakcja z klastrami usługi Service Fabric przy użyciu interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)
* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
