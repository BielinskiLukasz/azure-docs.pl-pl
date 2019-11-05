---
title: Skonfiguruj niestandardową nazwę domeny dla własnej hostowanej bramy usługi Azure API Management | Microsoft Docs
description: W tym temacie opisano procedurę konfigurowania niestandardowej nazwy domeny dla samodzielnej bramy usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1df2cce04021c1cd14c356311df921dd1c0298e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513811"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurowanie niestandardowej nazwy domeny

W przypadku aprowizacji [samodzielnie hostowanej bramy usługi Azure API Management](self-hosted-gateway-overview.md) nie jest ona przypisana do nazwy hosta i musi odwoływać się do niej adres IP. W tym artykule przedstawiono sposób mapowania istniejącej niestandardowej nazwy DNS (nazywanej także nazwą hosta) do bramy samoobsługowej.

> [!NOTE]
> Funkcja samoobsługowego bramy jest dostępna w wersji zapoznawczej. W wersji zapoznawczej Brama samoobsługowa jest dostępna tylko w warstwach deweloper i Premium bez dodatkowych opłat. Warstwa dewelopera jest ograniczona do jednego wdrożenia bramy samoobsługowego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować:

-   Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Wystąpienie API Management. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Brama samodzielna. Aby uzyskać więcej informacji, zobacz [jak zainicjować obsługę bramy samoobsługowej](api-management-howto-provision-self-hosted-gateway.md)
-   Niestandardowa nazwa domeny, do której należy użytkownik lub Twoja organizacja. Ten temat nie zawiera instrukcji dotyczących sposobu pozyskiwania niestandardowej nazwy domeny.
-   Rekord DNS hostowany na serwerze DNS, który mapuje niestandardową nazwę domeny na adres IP bramy samohostowanej. Ten temat nie zawiera instrukcji dotyczących hostowania rekordu DNS.
-   Musisz mieć prawidłowy certyfikat z kluczem publicznym i prywatnym (. PFX). Podmiot lub alternatywna nazwa podmiotu (SAN) musi być zgodna z nazwą domeny (umożliwia API Management wystąpieniu bezpiecznego ujawniania adresów URL za pośrednictwem protokołu SSL).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Dodawanie niestandardowego certyfikatu domeny do usługi API Management

1. W obszarze **zabezpieczenia**wybierz pozycję **Certyfikaty** .
2. Wybierz pozycję **+ Dodaj**.
3. Wprowadź nazwę zasobu dla certyfikatu w polu **ID** .
4. Wybierz plik zawierający certyfikat (. PFX), wybierając pole **certyfikatu** lub ikonę folderu obok niej.
5. Wprowadź hasło certyfikatu w polu **hasło** .
6. Wybierz pozycję **Utwórz** , aby dodać certyfikat do usługi API Management.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Użyj Azure Portal, aby ustawić niestandardową nazwę domeny dla bramy samoobsługowej

1. Wybierz **bramy** z sekcji **Ustawienia**.
2. Wybierz bramę samoobsługową, dla której chcesz skonfigurować nazwę domeny.
3. Wybierz pozycję **nazwy hostów** w obszarze **Ustawienia**.
4. Wybierz pozycję **+ Dodaj**
5. Wprowadź nazwę zasobu dla nazwy hosta w polu **Nazwa** .
6. Wprowadź nazwę domeny w polu Nazwa **hosta** .
7. Wybierz certyfikat z listy rozwijanej **certyfikatu** .
8. Zaznacz pole wyboru **Negocjuj certyfikat klienta** , jeśli którykolwiek z interfejsów API udostępnianych za pośrednictwem tej bramy używa uwierzytelniania certyfikatu klienta.
    > [!WARNING]
    > To ustawienie jest udostępniane przez wszystkie nazwy domen skonfigurowane dla bramy.
9. Wybierz pozycję **Dodaj** , aby przypisać niestandardową nazwę domeny do wybranej bramy samoobsługowej.

## <a name="next-steps"></a>Następne kroki

[Uaktualnianie i skalowanie usługi](upgrade-and-scale.md)
