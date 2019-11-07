---
title: Wykonywanie pakietów SQL Server Integration Services (SSIS) za pomocą narzędzia dtexec z obsługą platformy Azure
description: Dowiedz się, jak wykonywać pakiety SQL Server Integration Services (SSIS) za pomocą narzędzia dtexec z włączoną obsługą platformy Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9ab308d0e2145a0d0b40e8b37c8c5be07b55dac6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673554"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Uruchamianie pakietów SQL Server Integration Services za pomocą narzędzia dtexec z obsługą platformy Azure
W tym artykule opisano narzędzie wiersza polecenia dtexec (AzureDTExec) z obsługą platformy Azure. Służy do uruchamiania pakietów SQL Server Integration Services (SSIS) na Azure-SSIS Integration Runtime (IR) w Azure Data Factory.

Tradycyjne narzędzie dtexec zawiera SQL Server. Aby uzyskać więcej informacji, zobacz [Narzędzie dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Często są one wywoływane przez koordynatorów lub harmonogramów innych firm, takich jak ActiveBatch i Control-M, do uruchamiania pakietów SSIS w środowisku lokalnym. 

Nowoczesne narzędzie AzureDTExec zawiera narzędzie SQL Server Management Studio (SSMS). Może być również wywoływana przez koordynatorów lub harmonogramów innych firm do uruchamiania pakietów usług SSIS na platformie Azure. Ułatwia podnoszenie i przenoszenie pakietów usług SSIS i przenoszenie ich do chmury. Jeśli po migracji chcesz nadal korzystać z koordynatorów lub harmonogramów innych firm w codziennych operacjach, można teraz wywołać AzureDTExec zamiast dtexec.

AzureDTExec uruchamia pakiety jako działania wykonywania pakietów SSIS w potokach Data Factory. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS jako działań Azure Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

AzureDTExec można skonfigurować za pomocą programu SSMS, aby użyć aplikacji Azure Active Directory (Azure AD), która generuje potoki w fabryce danych. Można go również skonfigurować w taki sposób, aby uzyskiwać dostęp do systemów plików, udziałów plików lub Azure Files, w których przechowywane są pakiety. W oparciu o wartości, które dajesz dla opcji wywołania, AzureDTExec generuje i uruchamia unikatowy potok Data Factory za pomocą działania wykonaj pakiet SSIS. Wywoływanie AzureDTExec z tymi samymi wartościami dla jego opcji uruchamia ponownie istniejący potok.

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć AzureDTExec, Pobierz i zainstaluj najnowszą wersję programu SSMS, która jest w wersji 18,3 lub nowszej. Pobierz ją z [tej witryny sieci Web](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Konfigurowanie narzędzia AzureDTExec
Zainstalowanie programu SSMS na komputerze lokalnym spowoduje również zainstalowanie AzureDTExec. Aby skonfigurować ustawienia, uruchom program SSMS z opcją **Uruchom jako administrator** . Następnie wybierz pozycję **narzędzia** > **Migruj do platformy Azure** > **Skonfiguruj dtexec z obsługą platformy Azure**.

![Konfigurowanie menu dtexec z obsługą platformy Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Ta akcja powoduje otwarcie okna **AzureDTExecConfig** , które należy otworzyć z uprawnieniami administracyjnymi do zapisu w pliku *AzureDTExec. Settings* . Jeśli program SSMS nie został uruchomiony jako administrator, zostanie otwarte okno Kontrola konta użytkownika (UAC). Wprowadź hasło administratora, aby podwyższyć poziom uprawnień.

![Konfigurowanie ustawień dtexec z obsługą platformy Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

W oknie **AzureDTExecConfig** wprowadź następujące ustawienia konfiguracji:

- Identyfikator **aplikacji**: należy wprowadzić unikatowe identyfikatory aplikacji usługi Azure AD, którą tworzysz przy użyciu odpowiednich uprawnień, aby generować potoki w fabryce danych. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji usługi Azure AD i nazwy głównej usługi za pośrednictwem Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
- **AuthenticationKey**: Wprowadź klucz uwierzytelniania dla aplikacji usługi Azure AD.
- **TenantId**: wprowadź unikatowy identyfikator dzierżawy usługi Azure AD, w której jest tworzona aplikacja usługi Azure AD.
- Subskrypcja **: wprowadź**unikatowy identyfikator subskrypcji platformy Azure, w ramach której utworzono fabrykę danych.
- Grupa **zasobów: wprowadź**nazwę grupy zasobu platformy Azure, w której utworzono fabrykę danych.
- **DataFactory**: Wprowadź nazwę fabryki danych, w której będą generowane unikatowe potoki z uruchomionym działaniem pakietu SSIS, na podstawie wartości opcji dostarczonych podczas wywoływania AzureDTExec.
- **IRName**: wprowadź nazwę Azure-SSIS IR w fabryce danych, na której zostaną uruchomione pakiety określone w ścieżce Universal NAMING Convention (UNC) po wywołaniu AzureDTExec.
- **PackageAccessDomain**: Wprowadź poświadczenia domeny w celu uzyskania dostępu do pakietów w ścieżce UNC określonej podczas wywoływania AzureDTExec.
- **PackageAccessUserName**: Wprowadź poświadczenia nazwy użytkownika, aby uzyskać dostęp do pakietów w ścieżce UNC określonej podczas wywoływania AzureDTExec.
- **PackageAccessPassword**: Wprowadź poświadczenia hasła, aby uzyskać dostęp do pakietów w ścieżce UNC, która jest określona podczas wywoływania AzureDTExec.
- **LogPath**: wprowadź ścieżkę UNC folderu dziennika, w której będą zapisywane pliki dziennika z wykonywania pakietów na Azure-SSIS IR.
- **LogLevel**: Wprowadź wybrany zakres rejestrowania ze wstępnie zdefiniowanych opcji **null**, **Basic**, **verbose**lub **Performance** dla wykonań pakietów na Azure-SSIS IR.
- **LogAccessDomain**: Wprowadź poświadczenia domeny w celu uzyskania dostępu do folderu dziennika w ścieżce UNC podczas zapisywania plików dziennika, co jest wymagane, gdy określono **LogPath** , a **LogLevel** nie ma **wartości null**.
- **LogAccessUserName**: Wprowadź poświadczenia nazwy użytkownika, aby uzyskać dostęp do folderu dziennika w swojej ścieżce UNC podczas zapisywania plików dziennika, co jest wymagane, gdy określono **LogPath** i **LogLevel** nie ma **wartości null**.
- **LogAccessPassword**: Wprowadź poświadczenia hasła, aby uzyskać dostęp do folderu dziennika w jego ścieżce UNC podczas zapisywania plików dziennika, co jest wymagane, gdy określono **LogPath** , a **LogLevel** nie ma **wartości null**.
- **PipelineNameHashStrLen**: Wprowadź długość ciągów skrótu, które mają zostać wygenerowane z wartości opcji podanej podczas wywoływania AzureDTExec. Ciągi są używane do tworzenia unikatowych nazw dla potoków Data Factory, które uruchamiają pakiety na Azure-SSIS IR. Zwykle długość 32 znaków jest wystarczająca.

Aby przechowywać pakiety i pliki dziennika w lokalnych systemach plików lub udziałach plików, Dołącz do Azure-SSIS IR do sieci wirtualnej połączonej z siecią lokalną, aby umożliwić pobranie pakietów i zapisanie plików dziennika. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Aby uniknąć wyświetlania poufnych wartości zapisanych w pliku *AzureDTExec. Settings* w postaci zwykłego tekstu, kodujemy je do ciągów kodowania base64. Po wywołaniu AzureDTExec wszystkie ciągi kodowane algorytmem Base64 są zdekodowane z powrotem do ich oryginalnych wartości. Można bardziej zabezpieczyć plik *AzureDTExec. Settings* , ograniczając konta, które mają do niego dostęp.

## <a name="invoke-the-azuredtexec-utility"></a>Wywoływanie narzędzia AzureDTExec
Możesz wywołać AzureDTExec w wierszu polecenia i podać odpowiednie wartości dla konkretnych opcji w scenariuszu przypadków użycia.

Narzędzie jest instalowane w `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`. Możesz dodać ścieżkę do zmiennej środowiskowej "PATH", aby można ją było wywołać z dowolnego miejsca.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Wywołanie AzureDTExec oferuje podobne opcje jako wywołanie dtexec. Aby uzyskać więcej informacji, zobacz [Narzędzie dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Poniżej przedstawiono opcje, które są obecnie obsługiwane:

- **/F [Iku]** : ładuje pakiet, który jest przechowywany w systemie plików, udziale plików lub Azure Files. Jako wartość tej opcji można określić ścieżkę UNC do pliku pakietu w systemie plików, udziale plików lub Azure Files z rozszerzeniem dtsx. Jeśli określona ścieżka UNC zawiera spację, należy umieścić znaki cudzysłowu wokół całej ścieżki.
- **/Conf [igFile]** : Określa plik konfiguracji, z którego mają zostać wyodrębnione wartości. Korzystając z tej opcji, można ustawić konfigurację wykonawczą dla pakietu, która różni się od określonej w czasie projektowania. Można przechowywać różne ustawienia w pliku konfiguracji XML, a następnie ładować je przed wykonaniem pakietu. Aby uzyskać więcej informacji, zobacz [konfiguracje pakietów usług SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Aby określić wartość dla tej opcji, użyj ścieżki UNC do pliku konfiguracji w systemie plików, udziale plików lub Azure Files z rozszerzeniem dtsConfig. Jeśli określona ścieżka UNC zawiera spację, należy umieścić znaki cudzysłowu wokół całej ścieżki.
- **/Conn [zenia]** : określa parametry połączenia dla istniejących menedżerów połączeń w pakiecie. Za pomocą tej opcji można ustawić parametry połączenia w czasie wykonywania dla istniejących menedżerów połączeń w pakiecie, które różnią się od określonych w czasie projektowania. Określ wartość dla tej opcji w następujący sposób: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.
- **/Set**: zastępuje konfigurację parametru, zmiennej, właściwości, kontenera, dostawcy dziennika, modułu wyliczającego foreach lub połączenia w pakiecie. Tę opcję można określić wiele razy. Określ wartość dla tej opcji w następujący sposób: `property_path;value`. Na przykład `\package.variables[counter].Value;1` przesłania wartość zmiennej `counter` jako 1. Możesz użyć kreatora **konfiguracji pakietu** , aby znaleźć, skopiować i wkleić wartość `property_path` dla elementów w pakiecie, których wartość ma zostać przesłonięta. Aby uzyskać więcej informacji, zobacz [Kreator konfiguracji pakietu](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014).
- **/De [Crypt]** : ustawia hasło odszyfrowywania dla pakietu, który jest skonfigurowany przy użyciu poziomu ochrony **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** .

> [!NOTE]
> Wywoływanie AzureDTExec z nowymi wartościami dla jego opcji generuje nowy potok z wyjątkiem opcji **/de [kryptu]** .

## <a name="next-steps"></a>Następne kroki

Po wydaniu unikatowych potoków z działaniem pakietu usług SSIS i uruchomieniu po wywołaniu AzureDTExec można monitorować je w portalu Data Factory. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS jako działań Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

> [!WARNING]
> Wygenerowany potok powinien być używany tylko przez AzureDTExec. Jego właściwości lub parametry mogą ulec zmianie w przyszłości, dlatego nie należy ich modyfikować ani ponownie używać do innych celów. Modyfikacje mogą spowodować uszkodzenie AzureDTExec. W takim przypadku Usuń potoku. AzureDTExec generuje nowy potok przy następnym wywołaniu.
