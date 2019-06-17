---
title: 'Java SDK: Operacje systemu plików w usłudze Azure Data Lake magazynu Gen1 | Dokumentacja firmy Microsoft'
description: Użyj usługi Azure Storage Gen1 Java zestawu SDK Data Lake można wykonywać operacje systemu plików w Data Lake Storage Gen1, takie jak tworzenie folderów itp.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bc6e0718cdc4ccb18480dc760279da9c177db4cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60877468"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Operacje systemu plików w usłudze Azure Data Lake magazynu Gen1 przy użyciu zestawu SDK języka Java
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Zestaw SDK Java](data-lake-store-get-started-java-sdk.md)
> * [Interfejs API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Dowiedz się, jak używać usługi Azure Data Lake Storage Gen1 zestawu SDK Java, aby wykonywać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych itp. Aby uzyskać więcej informacji na temat Data Lake Storage Gen1 zobacz [usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md).

Możesz uzyskać dostęp dokumentacja interfejsu API zestawu SDK języka Java Data Lake Storage Gen1 na [dokumentacja interfejsu API usługi Azure Data Lake Storage Gen1 Java](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Wymagania wstępne
* Zestaw Java Development Kit (JDK 7 lub nowszy, korzystający z języka Java w wersji 1.7 lub nowszej)
* Konta usługi Data Lake Storage Gen1. Postępuj zgodnie z instrukcjami w artykule [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Ten samouczek używa programu Maven na potrzeby zależności między kompilacją i projektem. Chociaż istnieje możliwość kompilacji bez używania systemu kompilacji, takiego jak Maven lub Gradle, systemy te znacznie ułatwiają zarządzanie zależnościami.
* (Opcjonalnie) Wtyczka [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) lub [Eclipse](https://www.eclipse.org/downloads/) przypominająca środowisko IDE lub podobna.

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java
Przykładowy kod dostępny [w usłudze GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) przeprowadzi Cię przez proces tworzenia plików w magazynie, łączenia plików, pobierania pliku i usuwania niektórych plików z magazynu. Ta część artykułu przeprowadzi Cię przez najważniejsze fragmenty kodu.

1. Utwórz projekt Maven za pomocą polecenia [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) w wierszu polecenia lub w środowisku IDE. Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki IntelliJ, zobacz [tutaj](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki Eclipse, zobacz [tutaj](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Dodaj poniższe zależności do Twojego pliku **pom.xml** programu Maven. Dodaj poniższy fragment kodu przed tagiem **\</project>** :
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Pierwszą zależnością jest użycie zestawu SDK usługi Data Lake Storage Gen1 (`azure-data-lake-store-sdk`) z repozytorium maven. Drugą zależnością (`slf4j-nop`) jest określenie struktury rejestrowania używanej dla tej aplikacji. Korzysta z zestawu SDK usługi Data Lake Storage Gen1 [slf4j](https://www.slf4j.org/) fasady rejestrowania, które umożliwia wybór spośród wielu popularnych struktur rejestrowania, takich jak log4j, rejestrowanie Java, logback itp., lub Brak rejestrowania. W tym przykładzie wyłączyliśmy rejestrowanie, dlatego używamy powiązania **slf4j-nop**. Aby używać innych opcji rejestrowania w aplikacji, zobacz [tutaj](https://www.slf4j.org/manual.html#projectDep).

3. Dodaj do swojej aplikacji następujące instrukcje importowania.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Authentication

* Do uwierzytelniania użytkownika końcowego dla aplikacji, zobacz [końcowych-użytkowników — uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu języka Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Do uwierzytelniania service to service dla aplikacji, zobacz [uwierzytelniania Service to service za pomocą programu Data Lake Storage Gen1 przy użyciu języka Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-a-data-lake-storage-gen1-client"></a>Tworzenie klienta Data Lake Storage Gen1
Tworzenie [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) obiektu wymaga określenia nazwy konta Data Lake Storage Gen1 oraz dostawcy tokenu, który został wygenerowany podczas uwierzytelniania przy użyciu Data Lake Storage Gen1 (zobacz [uwierzytelniania](#authentication) sekcji). Nazwa konta Data Lake Storage Gen1 musi być w pełni kwalifikowanej nazwy domeny. Na przykład Zastąp ciąg **Fill-in-tutaj** wierszem podobnym **mydatalakestoragegen1.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Fragmenty kodu w poniższych sekcjach zawierają przykłady niektórych typowych operacji systemu plików. Zapoznanie się z pełną [dokumentacja interfejsu API Data Lake Storage Gen1 Java SDK](https://azure.github.io/azure-data-lake-store-java/javadoc/) z **ADLStoreClient** obiektu, aby zobaczyć inne operacje.

## <a name="create-a-directory"></a>Tworzenie katalogu

Poniższy fragment kodu tworzy strukturę katalogów w katalogu głównym konta Data Lake Storage Gen1 wskazana.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Tworzenie pliku

Poniższy fragment kodu tworzy plik (c.txt) w strukturze katalogów i zapisuje pewne dane do tego pliku.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Możesz również utworzyć plik (d.txt) przy użyciu tablic typu Byte.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

Definicja funkcji `getSampleContent` używana w poprzednim fragmencie kodu jest dostępna jako część przykładu [w serwisie GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Dołączanie do pliku

Poniższy fragment kodu dołącza zawartość do istniejącego pliku.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

Definicja funkcji `getSampleContent` używana w poprzednim fragmencie kodu jest dostępna jako część przykładu [w serwisie GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Odczytywanie pliku

Poniższy fragment kodu odczytuje zawartość z pliku w ramach konta Data Lake Storage Gen1.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Łączenie plików

Poniższy fragment kodu łączy dwa pliki na koncie usługi Data Lake Storage Gen1. W przypadku powodzenia połączony plik zastępuje te dwa istniejące pliki.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Zmienianie nazwy pliku

Poniższy fragment kodu zmienia nazwę pliku na koncie Data Lake Storage Gen1.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Pobranie metadanych dla pliku

Poniższy fragment kodu pobiera metadane dla pliku w ramach konta Data Lake Storage Gen1.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Ustawianie uprawnień do pliku

Poniższy fragment kodu ustawia uprawnienia do pliku, który został utworzony przez Ciebie w poprzedniej sekcji.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Poniższy fragment kodu rekursywnie wyświetla zawartość katalogu.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

Definicja funkcji `printDirectoryInfo` używana w poprzednim fragmencie kodu jest dostępna jako część przykładu [w serwisie GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Usuwanie plików i folderów

Poniższy fragment kodu usuwa określone pliki i foldery, w ramach konta Data Lake Storage Gen1 cyklicznie.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji
1. Aby przeprowadzić uruchomienie z poziomu środowiska IDE, znajdź i naciśnij przycisk **Run** (Uruchom). Aby przeprowadzić uruchomienie z poziomu programu Maven, użyj polecenia [exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Aby wygenerować autonomiczny plik JAR, który można uruchomić z poziomu wiersza polecenia, skompiluj plik JAR zawierający wszystkie zależności, używając [wtyczki zestawu Maven](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Pom.xml w [przykładowym kodzie źródłowym w serwisie GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) zawiera przykład.

## <a name="next-steps"></a>Kolejne kroki
* [Zapoznawanie się z dokumentacją JavaDoc dotyczącą zestawu SDK Java](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)


