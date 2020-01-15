---
title: Wyświetl matematyczne w czytniku immersyjny
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób wyświetlania matematycznych w czytniku immersyjny.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946123"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Jak wyświetlić matematykę w czytniku immersyjny

Czytnik immersyjny może wyświetlać dane matematyczne w postaci języka matematycznego Language Markup Language ([MathML](https://developer.mozilla.org/docs/Web/MathML)).
Typ MIME można ustawić za pomocą [fragmentu](../reference.md#chunk)czytnika immersyjny. Aby uzyskać więcej informacji, zobacz [obsługiwane typy MIME](../reference.md#supported-mime-types) .

## <a name="send-math-to-the-immersive-reader"></a>Wyślij Zapis matematyczny do czytnika immersyjny
Aby wysłać Zapis matematyczny do czytnika immersyjny, podaj fragment zawierający MathML i ustaw typ MIME na ```application/mathml+xml```;

Na przykład jeśli zawartość była następująca:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Następnie można wyświetlić zawartość przy użyciu poniższego języka JavaScript.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Po uruchomieniu czytnika immersyjny powinna zostać wyświetlona wartość:

![Matematyka w czytniku immersyjny](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Następne kroki

* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](../reference.md)