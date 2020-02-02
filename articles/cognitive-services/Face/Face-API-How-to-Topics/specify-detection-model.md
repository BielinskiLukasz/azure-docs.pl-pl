---
title: Jak określić model wykrywania — kroju
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób wybierania modelu wykrywania czołowego, który będzie używany z aplikacją platformy Azure.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934575"
---
# <a name="specify-a-face-detection-model"></a>Określanie modelu wykrywania twarzy

W tym przewodniku pokazano, jak określić model wykrywania kroju dla usługi Azure.

Usługa twarzy używa modeli uczenia maszynowego do wykonywania operacji na ludzkich twarzy na obrazach. Kontynuujemy ulepszanie dokładności naszych modeli w oparciu o opinie klientów i postępy w badaniach, a firma Microsoft zapewnia te udoskonalenia jako aktualizacje modelu. Deweloperzy mogą określić, która wersja modelu wykrywania kroju ma być używana; mogą wybrać model, który najlepiej pasuje do swojego przypadku użycia.

Przeczytaj, aby dowiedzieć się, jak określić model wykrywania czołowego w niektórych operacjach. Usługa kroju używa wykrywania czołowego za każdym razem, gdy konwertuje obraz obrazu do innej formy danych.

Jeśli nie masz pewności, czy należy używać najnowszego modelu, przejdź do sekcji [Oceń różne modele](#evaluate-different-models) , aby oszacować nowy model i porównać wyniki przy użyciu bieżącego zestawu danych.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zapoznać się z koncepcją wykrywania kroju systemu AI. Jeśli nie, zapoznaj się z przewodnikiem dotyczącego wykrywania koncepcji lub przewodnikiem:

* [Pojęcia dotyczące wykrywania czołowego](../concepts/face-detection.md)
* [Jak wykrywać twarze w obrazie](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Wykrywanie twarzy z określonym modelem

Wykrywanie twarzy umożliwia znalezienie obwiedni ludzkich i określenie ich punktów wizualizacji. Wyodrębnia funkcje funkcji i zapisuje je do późniejszego użycia w operacjach [rozpoznawania](../concepts/face-recognition.md) .

W przypadku korzystania z interfejsu API [wykrywanie kroju] i można przypisać wersję modelu z parametrem `detectionModel`. Dostępne są następujące wartości:

* `detection_01`
* `detection_02`

Adres URL żądania dla interfejsu API REST [Wykrywanie kroju] będzie wyglądać następująco:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

W przypadku korzystania z biblioteki klienta można przypisać wartość `detectionModel`, przekazując odpowiedni ciąg. Jeśli pozostawisz go nieprzypisane, interfejs API będzie używać domyślnej wersji modelu (`detection_01`). Zobacz Poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Dodaj miarę do osoby z określonym modelem

Usługa kroju może wyodrębnić dane z obrazu i skojarzyć je z obiektem **osoby** za pośrednictwem [osoby tworzącej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) interfejs API. W ramach tego wywołania interfejsu API można określić model wykrywania w taki sam sposób jak w przypadku [Wykrywanie kroju]na platformie.

Zobacz Poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Ten kod tworzy **odbiorcę** o identyfikatorze `mypersongroupid` i dodaje do niego **osobę** . Następnie dodaje do tej **osoby** nową miarę przy użyciu modelu `detection_02`. Jeśli nie określisz parametru *detectionModel* , interfejs API użyje domyślnego modelu, `detection_01`.

> [!NOTE]
> Nie musisz używać tego samego modelu wykrywania dla wszystkich twarzy w obiekcie **osoby** i nie musisz używać tego samego modelu wykrywania podczas wykrywania nowych twarzy do porównania z obiektem **osoby** (na przykład w interfejsie API rozpoznawania [Twarz — identyfikacja] ).

## <a name="add-face-to-facelist-with-specified-model"></a>Dodaj miarę do FaceList z określonym modelem

Możesz również określić model wykrywania, gdy dodasz miarę do istniejącego obiektu **FaceList** . Zobacz Poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Ten kod tworzy **FaceList** o nazwie `My face collection` i dodaje do niej miarę z modelem `detection_02`. Jeśli nie określisz parametru *detectionModel* , interfejs API użyje domyślnego modelu, `detection_01`.

> [!NOTE]
> Nie musisz używać tego samego modelu wykrywania dla wszystkich twarzy w obiekcie **FaceList** i nie musisz używać tego samego modelu wykrywania podczas wykrywania nowych twarzy do porównania z obiektem **FaceList** .

## <a name="evaluate-different-models"></a>Oceń różne modele

Różne modele wykrywania kroju są zoptymalizowane pod kątem różnych zadań. Zapoznaj się z poniższą tabelą, aby zapoznać się z omówieniem różnic.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Wybór domyślny dla wszystkich operacji wykrywania elementu czołowego. | Wydane w maju 2019 i dostępne opcjonalnie we wszystkich operacjach wykrywania czołowych.
|Nie zoptymalizowany pod kątem małych, bocznych i rozmytych twarzy.  | Ulepszona dokładność dla małych, bocznych i rozmytych twarzy. |
|Zwraca atrybuty kroju (ułożenie głowy, wiek, rozpoznawania emocji itd.), jeśli są one określone w wywołaniu wykrywania. |  Nie zwraca atrybutów kroju.     |
|Zwraca punkty orientacyjne, jeśli są one określone w wywołaniu wykrywania.   | Nie zwraca punktów orientacyjnych.  |

Najlepszym sposobem porównania wydajności `detection_01` i modeli `detection_02` jest użycie ich w przykładowym zestawie danych. Zalecamy wywoływanie interfejsu API [Wykrywanie kroju] na różnych obrazach, w szczególności obrazów wielu powierzchni lub twarzy, które trudno zobaczyć, przy użyciu poszczególnych modeli wykrywania. Zwróć uwagę na liczbę twarzy zwracanych przez poszczególne modele.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób określania modelu wykrywania, który ma być używany z różnymi interfejsami API rozpoznawania. Następnie postępuj zgodnie z przewodnikiem Szybki Start, aby rozpocząć korzystanie z wykrywania czołowego.

* [Zestaw SDK platformy .NET](../Quickstarts/csharp-sdk.md)
* [Zestaw SDK dla języka Python](../Quickstarts/python-sdk.md)
* [Zestaw SDK dla wejść](../Quickstarts/go-sdk.md)

[Wykrywanie kroju]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Twarz — identyfikacja]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
