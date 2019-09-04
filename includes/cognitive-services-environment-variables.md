---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274676"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurowanie zmiennej środowiskowej na potrzeby uwierzytelniania

Aplikacje muszą uwierzytelniać dostęp do Cognitive Services, z których korzystają. Aby przeprowadzić uwierzytelnianie, zalecamy utworzenie zmiennej środowiskowej do przechowywania kluczy zasobów platformy Azure. 

Po utworzeniu klucza należy zapisać go w nowej zmiennej środowiskowej na maszynie lokalnej, na której działa aplikacja. Aby ustawić zmienną środowiskową, otwórz okno konsoli i postępuj zgodnie z instrukcjami dla systemu operacyjnego. Zamień `your-key` na jeden z kluczy dla zasobu.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Po dodaniu zmiennej środowiskowej być może trzeba będzie ponownie uruchomić działające programy, które muszą odczytywać zmienną środowiskową, w tym okno konsoli. Na przykład jeśli używasz programu Visual Studio jako edytora, uruchom ponownie program Visual Studio przed uruchomieniem przykładu.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edytuj swój plik .bash_profile i dodaj zmienną środowiskową:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.

***