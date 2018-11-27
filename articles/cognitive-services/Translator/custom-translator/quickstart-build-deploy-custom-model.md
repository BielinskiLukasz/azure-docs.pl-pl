---
title: 'Szybki start: Tworzenie, wdrażanie i używanie modelu niestandardowego — rozszerzenie Custom Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start szczegółowo omówiono tworzenie systemu tłumaczenia przy użyciu rozszerzenia Custom Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: quickstart
ms.openlocfilehash: cb713ac53b399d8ba79d003ee6d948bb5af5220b
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975776"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Szybki start: Tworzenie, wdrażanie i używanie niestandardowego modelu tłumaczenia

Ten artykuł zawiera szczegółowe instrukcje tworzenia systemu tłumaczenia za pomocą rozszerzenia Custom Translator.

## <a name="prerequisites"></a>Wymagania wstępne

1. Aby używać portalu [Custom Translator](https://portal.customtranslator.azure.ai), musisz zalogować się na [konto Microsoft](https://signup.live.com) lub [konto usługi Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (konto organizacji hostowane na platformie Azure).

2. Subskrypcja interfejsu API tłumaczenia tekstu w usłudze Translator — w witrynie Azure Portal. Klucz subskrypcji interfejsu API tłumaczenia tekstu w usłudze Translator musisz skojarzyć z obszarem roboczym w rozszerzeniu Custom Translator. Zobacz instrukcje dotyczące [tworzenia konta umożliwiającego korzystanie z interfejsu API tłumaczenia tekstu w usłudze Translator](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Po spełnieniu obu powyższych warunków zaloguj się w portalu [Custom Translator](https://portal.customtranslator.azure.ai). W portalu Custom Translator przejdź do strony ustawień, na której możesz skojarzyć klucz subskrypcji interfejsu API tłumaczenia tekstu w usłudze Microsoft Translator z obszarem roboczym.

## <a name="create-a-project"></a>Tworzenie projektu

Na stronie docelowej portalu Custom Translator kliknij pozycję New Project (Nowy projekt). W oknie dialogowym możesz wprowadzić odpowiednią nazwę projektu, parę języków i kategorię, a także inne pola, które mają zastosowanie. Następnie zapisz projekt. Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie projektu](how-to-create-project.md).

![Tworzenie projektu](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Przekazywanie dokumentów

Następnie przekaż zestawy dokumentów dotyczących [szkolenia](training-and-model.md#training-dataset-for-custom-translator), [dostrajania](training-and-model.md#tuning-dataset-for-custom-translator) i [testowania](training-and-model.md#testing-dataset-for-custom-translator). Możesz przekazać zarówno dokumenty [równoległe](what-are-parallel-documents.md), jak i dokumenty kombinowane. Możesz również przekazać [słownik](what-is-dictionary.md).

Dokumenty można przekazywać na karcie dokumentów lub na stronie danego projektu.

![Przekazywanie dokumentów](media/quickstart/ct-how-to-upload.png)

Przekazując materiały, wybierz typ dokumentu (szkolenie, dostrajanie lub testowanie) oraz parę języków. W przypadku przekazywania dokumentów równoległych musisz dodatkowo podać nazwę dokumentu. Aby uzyskać więcej informacji, zobacz sekcję [Przekazywanie dokumentu](how-to-upload-document.md).

## <a name="create-a-model"></a>Tworzenie modelu

Po przekazaniu wszystkich wymaganych dokumentów należy utworzyć model.

Wybierz utworzony projekt. Pojawią się wszystkie przekazane dokumenty, których para języków jest zgodna z parą języków w projekcie. Wybierz dokumenty, które chcesz uwzględnić w modelu. Możesz wybrać dane [szkoleniowe](training-and-model.md#training-dataset-for-custom-translator), [dostrajania](training-and-model.md#tuning-dataset-for-custom-translator) i [testowe](training-and-model.md#testing-dataset-for-custom-translator) albo wskazać tylko dane szkoleniowe — rozszerzenie Custom Translator automatycznie utworzy zestaw dostrajania i zestaw testowy w modelu.

![Tworzenie modelu](media/quickstart/ct-how-to-train.png)

Po wybraniu odpowiednich dokumentów kliknij przycisk tworzenia modelu, aby utworzyć model i rozpocząć szkolenie. Na karcie modeli możesz sprawdzić stan szkolenia i szczegóły wszystkich wytrenowanych modeli.

Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie modelu](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analizowanie modelu

Po pomyślnym zakończeniu szkolenia sprawdź wyniki. Wynik BLEU jest metryką, która określa jakość tłumaczenia. Możesz również ręcznie porównać tłumaczenia uzyskane za pomocą modelu niestandardowego z tłumaczeniami zawartymi z zestawie testowym, przechodząc na kartę „Test”, a następnie klikając pozycję „System Results” (Wyniki systemu). Sprawdzenie kilku takich tłumaczeń pozwala wiarygodnie ustalić, jaka jest jakość tłumaczeń generowanych przez system. Aby uzyskać więcej informacji, zobacz sekcję [Wyniki testu systemu](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Wdrażanie przeszkolonego modelu

Gdy wszystko jest gotowe do wdrożenia przeszkolonego modelu, kliknij przycisk „Deploy” (Wdróż). W danym projekcie możesz mieć jeden wdrożony model. Stan wdrożenia możesz sprawdzić w kolumnie Status (Stan). Aby uzyskać więcej informacji, zobacz sekcję [Wdrożenie modelu](how-to-view-system-test-results.md#deploy-a-model).

![Wdrażanie przeszkolonego modelu](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Korzystanie z wdrożonego modelu

Dostęp do wdrożonych modeli można uzyskać za pośrednictwem [interfejsu API tłumaczenia tekstu w usłudze Microsoft Translator w wersji 3, podając identyfikator kategorii](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Więcej informacji na temat interfejsu API tłumaczenia tekstu w usłudze Translator można znaleźć na stronie internetowej z [dokumentacją interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak nawigować po [obszarze roboczym rozszerzenia Custom Translator i jak zarządzać projektami](workspace-and-project.md).
