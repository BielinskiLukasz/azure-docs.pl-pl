---
title: Samouczek — uzyskiwanie dostępu do obiektów blob magazynu w domenie niestandardowej usługi Azure CDN za pośrednictwem protokołu HTTPS | Microsoft Docs
description: ''
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: a1a86f28c9650c86df7c333ca52d772a09597efd
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091062"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Samouczek: uzyskiwanie dostępu do obiektów blob magazynu w domenie niestandardowej usługi Azure CDN za pośrednictwem protokołu HTTPS

Po zintegrowaniu konta magazynu platformy Azure z usługą Azure Content Delivery Network (CDN) możesz dodać domenę niestandardową i włączyć protokół HTTPS na tej domenie dla niestandardowego punktu końcowego magazynu obiektów blob. 

## <a name="prerequisites"></a>Wymagania wstępne

Zanim będzie możliwe wykonanie kroków opisanych w tym samouczku, najpierw należy zintegrować konto magazynu platformy Azure z usługą Azure CDN. Aby uzyskać więcej informacji, zobacz [Szybki start: integrowanie konta magazynu platformy Azure z usługą Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Dodawanie domeny niestandardowej
Po utworzeniu punktu końcowego usługi CDN w profilu nazwa punktu końcowego, czyli domena podrzędna domeny azureedge.net, jest uwzględniana w adresie URL w celu domyślnego dostarczania zawartości usługi CDN. Dostępna jest także opcja kojarzenia domeny niestandardowej z punktem końcowym usługi CDN. Ta opcja umożliwia dostarczanie zawartości przy użyciu domeny niestandardowej w adresie URL zamiast korzystania z nazwy punktu końcowego. Aby dodać domenę niestandardową do punktu końcowego, postępuj zgodnie z instrukcjami w tym samouczku: [Dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Konfigurowanie protokołu HTTPS
Korzystając z protokołu HTTPS w domenie niestandardowej masz pewność, że dane są przesyłane przez Internet w bezpieczny sposób przy użyciu szyfrowania TLS/SSL. Gdy przeglądarka internetowa łączy się z witryną internetową za pośrednictwem protokołu HTTPS, sprawdza, czy certyfikat zabezpieczeń witryny internetowej jest poprawny i czy został wystawiony przez autentyczny urząd certyfikacji. Aby skonfigurować protokół HTTPS w domenie niestandardowej, postępuj zgodnie z instrukcjami w tym samouczku: [Konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego
Jeśli konfiguracja punktu końcowego magazynu obiektów blob nie zezwala na anonimowy dostęp w trybie odczytu, należy podać token [sygnatury dostępu współdzielonego (SAS)](cdn-sas-storage-support.md) w każdym żądaniu wysłanym do domeny niestandardowej. Domyślnie punkty końcowe magazynu obiektów blob nie zezwalają na anonimowy dostęp w trybie odczytu. Aby uzyskać więcej informacji o sygnaturze SAS, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../storage/blobs/storage-manage-access-to-resources.md).

Usługa Azure CDN ignoruje wszelkie ograniczenia dodane do tokenu SAS. Na przykład wszystkie tokeny SAS mają czas wygaśnięcia, co oznacza, że wciąż można uzyskać dostęp do zawartości przy użyciu wygasłej sygnatury SAS do momentu wyczyszczenia zawartości z serwerów punktu obecności (POP) usługi CDN. Możesz kontrolować, jak długo dane są buforowane w usłudze Azure CDN, ustawiając nagłówek odpowiedzi pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Zarządzanie wygasaniem obiektów blob usługi Azure Storage w usłudze Azure CDN](cdn-manage-expiration-of-blob-content.md).

Jeśli tworzysz wiele adresów URL sygnatur SAS dla tego samego punktu końcowego obiektu blob, rozważ włączenie buforowania ciągu zapytania. Daje to gwarancję, że każdy adres URL jest traktowany jako unikatowy obiekt. Aby uzyskać więcej informacji, zobacz [Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Przekierowywanie protokołu HTTP do HTTPS
Możesz przekierowywać ruch HTTP do ruchu HTTPS, tworząc [regułę przekierowywania adresu URL](cdn-rules-engine-reference-features.md#url-redirect) przy użyciu [aparatu reguł usługi Azure CDN](cdn-rules-engine.md). Ta opcja wymaga profilu **usługi Azure CDN w warstwie Premium firmy Verizon**. 

![Reguła przekierowywania adresu URL](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

W tej regule właściwość *Cdn-endpoint-name* odwołuje się do nazwy skonfigurowanej dla punktu końcowego usługi CDN, którą można wybrać z listy rozwijanej. Wartość właściwości *origin-path* odnosi się do ścieżki w ramach konta magazynu źródła, w którym znajduje się zawartość statyczna. Jeśli hostujesz całą zawartość statyczną w jednym kontenerze, zastąp właściwość *origin-path* nazwą tego kontenera.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia
Gdy uzyskujesz dostęp do obiektów blob za pomocą usługi Azure CDN, opłaty są naliczane według [cen magazynu obiektów blob](https://azure.microsoft.com/pricing/details/storage/blobs/) za ruch między serwerami POP a źródłem (magazynem obiektów blob) oraz według [cennika usługi Azure CDN](https://azure.microsoft.com/pricing/details/cdn/) za dane, do których uzyskano dostęp z serwerów POP.

Jeśli na przykład masz konto magazynu w Stanach Zjednoczonych, do którego dostęp jest uzyskiwany przy użyciu usługi Azure CDN, a ktoś w Europie spróbuje uzyskać dostęp do jednego z obiektów blob w tym koncie magazynu za pośrednictwem usługi Azure CDN, usługa Azure CDN najpierw sprawdzi punkt POP położony najbliżej Europy w poszukiwaniu tego obiektu blob. Jeśli zostanie znaleziony, usługa Azure CDN uzyska dostęp do kopii tego obiektu blob i zastosuje cennik usługi CDN, ponieważ dostęp będzie uzyskiwany w usłudze Azure CDN. Jeśli nie zostanie znaleziony, usługa Azure CDN skopiuje obiekt blob do serwera POP, co spowoduje wystąpienie ruchu wychodzącego i naliczenie opłat za transakcje zgodnie z cennikiem magazynu obiektów blob, a następnie uzyska dostęp do pliku na serwerze POP, co spowoduje naliczenie opłat za usługę Azure CDN.

## <a name="next-steps"></a>Następne kroki
[Samouczek: ustawianie reguł buforowania usługi Azure CDN](cdn-caching-rules-tutorial.md)




