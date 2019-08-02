---
title: 'Szybki start: Biblioteka kliencka programu Front dla języka Python | Microsoft Docs'
description: Rozpocznij pracę z biblioteką klienta programu Front dla języka Python...
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: pafarley
ms.openlocfilehash: 011345cca1ed1c763a628c94401320862182c9cc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707369"
---
# <a name="quickstart-face-client-library-for-python"></a>Szybki start: Biblioteka kliencka programu Front dla języka Python

Rozpocznij pracę z biblioteką klienta programu Front dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa interfejs API rozpoznawania twarzy zapewnia dostęp do zaawansowanych algorytmów służących do wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj biblioteki klienta programu Front dla języka Python, aby:

* Wykrywanie twarzy na obrazie
* Wyszukiwanie podobnych twarzy
* Tworzenie i uczenie grupy osób
* Identyfikowanie kroju
* Utwórz migawkę migracji danych

[](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Przykłady](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=Face&sort=0) pakietu | [kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)[](https://pypi.org/project/azure-cognitiveservices-vision-face/)źródłowego biblioteki dokumentacji referencyjnej (PiPy) | 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-face-azure-resource"></a>Tworzenie zasobu platformy Azure w postaci czołowej

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla konkretnej aplikacji przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni bezpłatnie. Po utworzeniu konta będzie ono dostępne w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/)

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy skrypt&mdash;w języku Python, na przykład*QuickStart-File.py*. Następnie otwórz go w preferowanym edytorze lub środowisku IDE i zaimportuj poniższe biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Następnie utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. Może zajść konieczność zmiany pierwszej części punktu końcowego (`westus`) w celu dopasowania do subskrypcji.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej.

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Bibliotekę kliencką można zainstalować za pomocą programu:

```console
pip install --upgrade azure-cognitiveservices-Face
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre z najważniejszych funkcji zestawu SDK języka Python.

|Name (Nazwa)|Opis|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Ta klasa reprezentuje autoryzację do korzystania z usługi twarz i jest potrzebna dla wszystkich funkcji funkcjonalnych. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonywać przy użyciu twarzy ludzkich. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej części obrazu. Można go użyć do pobierania szczegółowych informacji o kroju.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Ta klasa zarządza opartymi na chmurze konstrukcjami **FaceList** , w których jest przechowywany asortyment zestawu twarzy. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Ta klasa zarządza konstrukcjami **osób** , które są przechowywane w chmurze, które przechowują zestaw twarzy należących do jednej osoby.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Ta klasa **zarządza konstrukcjami** , które są przechowywane w chmurze, przechowujących zestaw obiektów **osób** . |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Ta klasa zarządza funkcją migawek; można jej użyć do tymczasowego zapisania wszystkich danych platformy opartej na chmurze i przeprowadzenia migracji tych danych do nowej subskrypcji platformy Azure. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienckiej Front dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)
* [Utwórz migawkę migracji danych](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start założono, że [utworzono zmienną środowiskową](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla `FACE_SUBSCRIPTION_KEY`klucza czołowego o nazwie.

Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Poniższy kod wykrywa czołową w obrazie zdalnym. Spowoduje to wydrukowanie z konsoli programu wykrytego identyfikatora, a także zapisanie jej w pamięci w programie. Następnie wykrywa twarze w obrazie z wieloma osobami i drukuje ich identyfikatory również w konsoli. Zmieniając parametry w metodzie [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) , można zwrócić różne informacje z każdym obiektem [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) , aby poznać więcej scenariuszy wykrywania.

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedyncze wykryte twarze i przeszukuje zestaw innych twarzy, aby znaleźć dopasowania. Po znalezieniu dopasowania program drukuje współrzędne prostokąta dopasowanej powierzchni do konsoli. 

### <a name="find-matches"></a>Znajdź dopasowania

Najpierw należy uruchomić kod w powyższej sekcji ([wykrywaj twarze na obrazie](#detect-faces-in-an-image)), aby zapisać odwołanie do jednej twarzy. Następnie uruchom następujący kod, aby uzyskać odwołania do kilku twarzy w obrazie grupy.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Następnie Dodaj poniższy blok kodu, aby znaleźć wystąpienia pierwszej czołowej w grupie. Zobacz metodę [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) , aby dowiedzieć się, jak zmodyfikować to zachowanie.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Drukuj dopasowania

Użyj poniższego kodu, aby wydrukować szczegóły dopasowania do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Tworzenie i uczenie grupy osób

Poniższy kod tworzy obiekt **Person** z trzema różnymi obiektami **osób** . Kojarzy każdej **osoby** z zestawem przykładowych obrazów, a następnie pociąga za sobą możliwość rozpoznawania każdej osoby. 

### <a name="create-persongroup"></a>Utwórz osobę

Aby wykonać czynności opisane w tym scenariuszu, należy zapisać następujące obrazy w katalogu głównym projektu: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Ta grupa obrazów zawiera trzy zestawy obrazów kroju odpowiadających trzem różnym osobom. Kod określi trzy obiekty **osób** i skojarzy je z plikami obrazów, które zaczynają `woman`się `man`od, `child`i.

Po skonfigurowaniu obrazów Zdefiniuj etykietę w górnej części skryptu **dla obiektu,** który utworzysz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Następnie Dodaj następujący kod w dolnej części skryptu. Ten kod tworzy **PersongGroup** i trzy obiekty **osób** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Przypisywanie twarzy do osób

Poniższy kod sortuje obrazy według ich prefiksu, wykrywa twarze i przypisuje powierzchnie do każdego obiektu **osoby** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Szkolenie zespołu

Po przypisaniu twarzy należy przeszkolić **osobę** , aby mogła ona identyfikować funkcje wizualne powiązane z poszczególnymi obiektami **osób** . Poniższy kod wywołuje metodę synchronicznego pouczenia i sonduje wynik, drukując stan do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identyfikowanie kroju

Poniższy kod pobiera obraz z wieloma twarzami i szuka tożsamości każdej osoby w obrazie. Porównuje każdą z wykrytych twarzy do obiektu należącego do **użytkownika**, bazy danych różnych **osób** , z którymi każda z nich ma wiele twarzy. 

> [!IMPORTANT]
> Aby można było uruchomić ten przykład, należy najpierw uruchomić kod w temacie [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Pobierz obraz testowy

Poniższy kod wygląda w folderze głównym projektu dla obrazu _test-Image-Person-Group. jpg_ i wykrywa twarze na obrazie. Ten obraz można znaleźć za pomocą obrazów używanych do zarządzania **osobami** : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identyfikowanie twarzy

Metoda **Zidentyfikuj** pobiera tablicę wykrytych twarzy i porównuje je z tą **osobą**. Jeśli może być zgodna z wykrytą stroną do **osoby**, zapisuje wynik. Ten kod drukuje szczegółowe wyniki dopasowania do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Utwórz migawkę migracji danych

Funkcja migawek pozwala przenosić zapisane dane, takie jak wyszkolone **osoby**, do innej subskrypcji platformy Azure Cognitive Services. Możesz chcieć użyć tej funkcji, jeśli na przykład utworzono obiekt obiektu **osoby** z użyciem bezpłatnej subskrypcji próbnej, a teraz chcesz przeprowadzić migrację go do płatnej subskrypcji. Więcej informacji na temat funkcji migawek można znaleźć w temacie [Migrowanie danych własnych](../Face-API-How-to-Topics/how-to-migrate-face-data.md) .

W tym przykładzie zostanie przeprowadzona migracja grupy **osób** utworzonych w temacie [Tworzenie i uczenie osoby](#create-and-train-a-person-group). Możesz najpierw wykonać tę sekcję lub użyć własnych konstrukcji danych czołowych.

### <a name="set-up-target-subscription"></a>Skonfiguruj subskrypcję docelową

Najpierw musisz mieć drugą subskrypcję platformy Azure z zasobem czołowym; można to zrobić, wykonując czynności opisane w sekcji [Konfigurowanie](#setting-up) . 

Następnie Utwórz następujące zmienne w górnej części skryptu. Należy również utworzyć nowe zmienne środowiskowe dla identyfikatora subskrypcji Twojego konta platformy Azure, a także klucz i Identyfikator subskrypcji nowego konta (docelowego). 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Uwierzytelnianie klienta docelowego

Później w skrypcie Zapisz bieżący obiekt klienta jako klienta źródłowego, a następnie Uwierzytelnij nowy obiekt klienta dla subskrypcji docelowej. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Użyj migawki

Pozostałe operacje tworzenia migawek odbywają się w ramach funkcji asynchronicznej. 

1. Pierwszym krokiem jest **wykonanie** migawki, która zapisuje dane pierwotnej subskrypcji w tymczasowej lokalizacji w chmurze. Ta metoda zwraca identyfikator używany do wykonywania zapytań dotyczących stanu operacji.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Następnie wykonaj zapytanie o identyfikator do momentu zakończenia operacji.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Ten kod wykorzystuje `wait_for_operation` funkcję, którą należy zdefiniować osobno:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Wróć do funkcji asynchronicznej. Użyj operacji **Zastosuj** , aby zapisać swoje dane z Twojej subskrypcji docelowej. Ta metoda zwraca również identyfikator.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Ponownie Użyj `wait_for_operation` funkcji, aby zbadać identyfikator do momentu zakończenia operacji.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Po wykonaniu tych kroków będziesz mieć możliwość uzyskania dostępu do konstrukcji danych własnych z nowej subskrypcji (docelowej).

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `python` polecenia w pliku szybkiego startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Jeśli utworzono **osobę** z tego przewodnika Szybki Start i chcesz ją usunąć, uruchom następujący kod w skrypcie:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

W przypadku migrowania danych przy użyciu funkcji Snapshot w tym przewodniku Szybki Start należy również usunąć **osobę** , która została zapisana do subskrypcji docelowej.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku szybki start pokazano, jak wykonywać zadania podstawowe przy użyciu biblioteki kroju dla języka Java. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie interfejs API rozpoznawania twarzy (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Co to jest interfejs API rozpoznawania twarzy?](../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).