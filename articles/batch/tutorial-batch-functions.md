---
title: Wyzwalanie zadania usługi Batch przy użyciu Azure Functions
description: Samouczek — stosowanie OCR do zeskanowanych dokumentów w miarę ich dodawania do obiektu blob magazynu
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: 99b9a8cac1342f30fcd70f8e938ba6d3dcb90aef
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770156"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Samouczek: wyzwalanie zadania usługi Batch przy użyciu Azure Functions

W tym samouczku dowiesz się, jak wyzwolić zadanie usługi Batch przy użyciu Azure Functions. Przeprowadzimy przykład, w którym dokumenty dodane do kontenera obiektów BLOB usługi Azure Storage mają do nich zastosowanie optycznego rozpoznawania znaków (OCR) za pośrednictwem Azure Batch. Aby usprawnić przetwarzanie OCR, skonfigurujemy funkcję platformy Azure, która uruchamia zadanie OCR do przetwarzania wsadowego za każdym razem, gdy plik zostanie dodany do kontenera obiektów BLOB.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Batch i połączone konto usługi Azure Storage. Aby uzyskać więcej informacji na temat tworzenia i łączenia kont, zobacz [Tworzenie konta usługi Batch](quick-create-portal.md#create-a-batch-account) .
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do [portalu Azure](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Tworzenie puli usługi Batch i zadania wsadowego za pomocą Batch Explorer

W tej sekcji użyjesz Batch Explorer do utworzenia puli partii i zadania wsadowego, które będą uruchamiać zadania OCR. 

### <a name="create-a-pool"></a>Tworzenie puli

1. Zaloguj się, aby Batch Explorer przy użyciu poświadczeń platformy Azure.
1. Utwórz pulę, wybierając pozycję **Pule** na pasku po lewej stronie, a następnie przycisk **Dodaj** nad formularzem wyszukiwania. 
    1. Wybierz identyfikator i nazwę wyświetlaną. Dla tego przykładu będziemy używać `ocr-pool`.
    1. Ustaw typ skali na **stały rozmiar**i ustaw liczbę węzłów dedykowanych na 3.
    1. Wybierz pozycję **Ubuntu 18,04-LTS** jako system operacyjny.
    1. Wybierz `Standard_f2s_v2` jako rozmiar maszyny wirtualnej.
    1. Włącz zadanie uruchamiania i Dodaj `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`polecenie. Pamiętaj, aby ustawić tożsamość użytkownika jako **domyślnego użytkownika zadania (administratora)** , co umożliwia uruchamianie zadań w celu uwzględnienia poleceń z `sudo`.
    1. Kliknij przycisk **OK**.
### <a name="create-a-job"></a>Tworzenie zadania

1. Utwórz zadanie w puli, wybierając pozycję **zadania** na pasku po lewej stronie, a następnie przycisk **Dodaj** nad formularzem wyszukiwania. 
    1. Wybierz identyfikator i nazwę wyświetlaną. Dla tego przykładu będziemy używać `ocr-job`.
    1. Ustaw pulę na `ocr-pool`lub inną nazwę wybraną dla puli.
    1. Kliknij przycisk **OK**.


## <a name="create-blob-containers"></a>Tworzenie kontenerów obiektów BLOB

Tutaj utworzysz kontenery obiektów blob, które będą przechowywać pliki wejściowe i wyjściowe dla zadania wsadowego OCR.

1. Zaloguj się, aby Eksplorator usługi Storage przy użyciu poświadczeń platformy Azure.
1. Korzystając z konta magazynu połączonego z kontem usługi Batch, Utwórz dwa kontenery obiektów BLOB (jeden dla plików wejściowych, jeden dla plików wyjściowych), wykonując czynności opisane w [sekcji Tworzenie kontenera obiektów BLOB](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

W tym przykładzie kontener wejściowy ma nazwę `input` i jest miejsce, gdzie wszystkie dokumenty bez OCR są początkowo przeładowane do przetwarzania. Kontener wyjściowy ma nazwę `output` i polega na tym, że zadanie wsadowe zapisuje przetworzone dokumenty z użyciem OCR.  
    * W tym przykładzie wywołamy nasze `input`kontenera wejściowego i nasz `output`kontenera wyjściowego.  
    * Kontener wejściowy to miejsce, w którym wszystkie dokumenty bez OCR są początkowo przekazywane.  
    * Kontener danych wyjściowych to miejsce, w którym zadanie wsadowe zapisuje dokumenty przy użyciu OCR.  

Utwórz sygnaturę dostępu współdzielonego dla kontenera danych wyjściowych w Eksplorator usługi Storage. W tym celu kliknij prawym przyciskiem myszy kontener danych wyjściowych i wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego..** .. W obszarze **uprawnienia**Sprawdź pozycję **Zapisz**. Nie są wymagane żadne inne uprawnienia.  

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

W tej sekcji utworzysz funkcję platformy Azure, która wyzwala zadanie wsadowe OCR za każdym razem, gdy plik zostanie przekazany do kontenera wejściowego.

1. Wykonaj kroki opisane w temacie [Tworzenie funkcji wyzwalanej przez usługę Azure Blob Storage](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) , aby utworzyć funkcję.
    1. Po wyświetleniu monitu o konto magazynu Użyj tego samego konta magazynu, które zostało połączone z kontem usługi Batch.
    1. W obszarze **stos środowiska uruchomieniowego**wybierz opcję .NET. Zapiszemy naszą funkcję w programie C# , aby korzystać z zestawu SDK programu .NET w usłudze Batch.
1. Po utworzeniu funkcji wyzwalanej przez obiekt BLOB Użyj [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) i [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) z usługi GitHub w funkcji.
    * `run.csx` jest uruchamiany po dodaniu nowego obiektu BLOB do wejściowego kontenera obiektów BLOB.
    * `function.proj` wyświetla listę zewnętrznych bibliotek w kodzie funkcji, na przykład zestaw SDK usługi Batch dla platformy .NET.
1. Zmień wartości zastępcze zmiennych w funkcji `Run()` pliku `run.csx`, aby odzwierciedlały poświadczenia dotyczące partii i magazynu. Poświadczenia konta magazynu i partii można znaleźć w Azure Portal w sekcji **klucze** Twojego konta w usłudze Batch.
    * Pobierz poświadczenia konta magazynu i partii w Azure Portal w sekcji **klucze** Twojego konta w usłudze Batch. 

## <a name="trigger-the-function-and-retrieve-results"></a>Wyzwalanie funkcji i pobieranie wyników

Przekaż wszystkie zeskanowane pliki z katalogu [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) w witrynie GitHub do kontenera wejściowego. Monitoruj Batch Explorer, aby potwierdzić, że zadanie zostało dodane do `ocr-pool` dla każdego pliku. Po kilku sekundach plik z zastosowaniem OCR zostanie dodany do kontenera danych wyjściowych. Plik jest następnie widoczny i możliwy do pobierania na Eksplorator usługi Storage.

Ponadto możesz obejrzeć plik dzienników w dolnej części okna edytora sieci Web Azure Functions, w którym będą wyświetlane komunikaty podobne do każdego pliku, który został przekazany do kontenera wejściowego:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Aby pobrać pliki wyjściowe z Eksplorator usługi Storage na maszynę lokalną, najpierw wybierz pliki, które chcesz, a następnie wybierz pozycję **Pobierz** na najwyższej Wstążce. 

> [!TIP]
> Pobrane pliki są przeszukiwane, jeśli są otwarte w czytniku PDF.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie pul i zadań za pomocą Batch Explorer
> * Używanie Eksplorator usługi Storage do tworzenia kontenerów obiektów blob i sygnatury dostępu współdzielonego (SAS)
> * Tworzenie funkcji platformy Azure wyzwalanej przez obiekt BLOB
> * Przekazywanie plików wejściowych do usługi Storage
> * Monitorowanie wykonania zadań podrzędnych
> * Pobieranie plików wyjściowych

* Aby uzyskać więcej przykładów dotyczących planowania i przetwarzania obciążeń usługi Batch przy użyciu interfejsu API platformy .NET, zobacz [przykłady w witrynie GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Aby wyświetlić więcej Azure Functions wyzwalaczy, których można użyć do uruchamiania obciążeń wsadowych, zapoznaj [się z dokumentacją Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
