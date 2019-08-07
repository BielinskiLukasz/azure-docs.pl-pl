---
title: Element interfejsu użytkownika usługi Azure CredentialsCombo | Microsoft Docs
description: Opisuje element interfejsu użytkownika Microsoft. COMPUTE. CredentialsCombo dla Azure Portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: 3036e5882e236dbbb9cf4f9aae17617822422a82
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742104"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI element
Grupa kontrolek z wbudowaną walidacją haseł systemu Windows i Linux oraz kluczy publicznych SSH.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

W przypadku systemu Windows użytkownicy widzą następujące tematy:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Dla systemu Linux z wybranym hasłem użytkownicy zobaczą:

![Hasło Microsoft. COMPUTE. CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Dla systemu Linux z wybranym kluczem publicznym SSH użytkownicy zobaczą:

![Microsoft. COMPUTE. CredentialsCombo — klucz systemu Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schemat
W przypadku systemu Windows należy użyć następującego schematu:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

W przypadku systemu **Linux**należy użyć następującego schematu:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- `osPlatform`musi być określony i może być w **systemie Windows** lub **Linux**.
- Jeśli `constraints.required` jest ustawiona na **true**, hasło lub pola tekstowe klucza publicznego SSH muszą mieć wartości, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **true**.
- Jeśli `options.hideConfirmation` jest ustawiona na **wartość true**, drugie pole tekstowe potwierdzające hasło użytkownika jest ukryte. Wartość domyślna to **false**.
- Jeśli `options.hidePassword` jest ustawiona na **true**, opcja używania uwierzytelniania hasła jest ukryta. Może być używana tylko wtedy, `osPlatform` gdy jest to system **Linux**. Wartość domyślna to **false**.
- Dodatkowe ograniczenia dotyczące dozwolonych haseł można zaimplementować przy użyciu `customPasswordRegex` właściwości. Ciąg w jest `customValidationMessage` wyświetlany w przypadku niepowodzenia niestandardowej walidacji hasła. Wartość domyślna dla obu właściwości jest **równa null**.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Jeśli `osPlatform` jest **Windows**lub `osPlatform` jest systemem **Linux** , a użytkownik podał hasło zamiast klucza publicznego SSH, kontrolka zwróci następujące dane wyjściowe:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Jeśli `osPlatform` jest **systemem Linux** , a użytkownik podał klucz publiczny SSH, kontrolka zwróci następujące dane wyjściowe:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
