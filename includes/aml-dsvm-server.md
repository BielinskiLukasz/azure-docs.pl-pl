---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302205"
---
1. Wykonaj czynności opisane w [przewodniku Szybki start dotyczącym języka Python dla usługi Azure Machine Learning](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) w celu utworzenia obszaru roboczego.  Jeśli chcesz, możesz pominąć sekcję **Korzystanie z notesu**.
1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. Dodaj plik konfiguracji obszaru roboczego przy użyciu jednej z następujących metod:
    * Do sklonowanego katalogu skopiuj plik **aml_config\config.json** utworzony podczas wykonywania czynności z przewodnika Szybki start stanowiącego wymagania wstępne.
    * Utwórz nowy obszar roboczy za pomocą kodu w notesie [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) w sklonowanym katalogu.
1. Uruchom serwer notesów z poziomu sklonowanego katalogu.
    
    ```shell
    jupyter notebook
    ```