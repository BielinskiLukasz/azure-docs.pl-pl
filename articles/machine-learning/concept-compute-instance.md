---
title: Co to jest wystąpienie obliczeniowe usługi Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat wystąpienia obliczeniowego Azure Machine Learning — w pełni zarządzanej stacji roboczej opartej na chmurze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: f4938d517d9a5c244045798a79f31b96bacd03f5
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829445"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Co to jest wystąpienie obliczeniowe usługi Azure Machine Learning?

Wystąpienie obliczeniowe Azure Machine Learning to zarządzana stacja robocza oparta na chmurze dla analityków danych.

Wystąpienia obliczeniowe ułatwiają rozpoczęcie pracy z programowaniem Azure Machine Learning, a także zapewnianie możliwości zarządzania i gotowości przedsiębiorstwa dla administratorów IT.  

Użyj wystąpienia obliczeniowego jako w pełni skonfigurowanego i zarządzanego środowiska programistycznego w chmurze na potrzeby uczenia maszynowego. Mogą one również służyć jako obiekt docelowy obliczeń na potrzeby szkoleń i inferencing na potrzeby tworzenia i testowania.  

W przypadku szkolenia modelu klasy produkcyjnej Użyj [Azure Machine Learning klastra obliczeniowego](how-to-set-up-training-targets.md#amlcompute) z możliwościami skalowania wielowęzłowego. W celu wdrożenia modelu klasy produkcyjnej należy użyć [klastra usługi Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Dlaczego warto używać wystąpienia obliczeniowego?

Wystąpienie obliczeniowe to w pełni zarządzana stacja robocza oparta na chmurze zoptymalizowana pod kątem środowiska projektowego uczenia maszynowego. Zapewnia następujące korzyści:

|Najważniejsze korzyści|Opis|
|----|----|
|Produktywność|Możesz tworzyć i wdrażać modele przy użyciu zintegrowanych notesów i następujących narzędzi w programie Azure Machine Learning Studio:<br/>-Jupyter<br/>- JupyterLab<br/>-RStudio (wersja zapoznawcza)<br/>Wystąpienie obliczeniowe jest w pełni zintegrowane z Azure Machine Learning obszarem roboczym i Studio. Notesy i dane można udostępniać innym analitykom danych w obszarze roboczym. Możesz również skonfigurować VS Code zdalne Programowanie przy użyciu protokołu [SSH](how-to-set-up-vs-code-remote.md) |
|Zarządzane & bezpieczne|Zmniejsz poziom bezpieczeństwa i Dodaj zgodność z wymaganiami dotyczącymi zabezpieczeń przedsiębiorstwa. Wystąpienia obliczeniowe zapewniają niezawodne zasady zarządzania i bezpieczne konfiguracje sieci, takie jak:<br/><br/>— Autoinicjowanie obsługi z szablonów Menedżer zasobów lub Azure Machine Learning SDK<br/>- [Kontrola dostępu oparta na rolach (Azure RBAC)](/azure/role-based-access-control/overview)<br/>- [Obsługa sieci wirtualnej](how-to-enable-virtual-network.md#compute-instance)<br/>-Zasady protokołu SSH do włączania/wyłączania dostępu SSH<br/>Włączono protokół TLS 1,2 |
|Wstępnie skonfigurowane &nbsp; dla &nbsp; ml|Oszczędź czas na zadaniach instalacyjnych ze wstępnie skonfigurowanymi i aktualnymi pakietami ML, platformami uczenia głębokiego, sterownikami procesora GPU.|
|W pełni dostosowywalne|Szeroka pomoc techniczna dla typów maszyn wirtualnych platformy Azure, w tym GPU i utrwalone dostosowanie niskiego poziomu, takie jak instalowanie pakietów i sterowników, sprawia, że zaawansowane scenariusze to Breeze. |

## <a name="tools-and-environments"></a><a name="contents"></a>Narzędzia i środowiska

> [!IMPORTANT]
> Narzędzia oznaczone poniżej (wersja zapoznawcza) są obecnie dostępne w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wystąpienie obliczeniowe Azure Machine Learning umożliwia tworzenie, uczenie i wdrażanie modeli w pełni zintegrowane środowisko notesu w obszarze roboczym.

Te narzędzia i środowiska są zainstalowane w wystąpieniu obliczeniowym: 

|Narzędzia ogólne & środowiska|Szczegóły|
|----|:----:|
|Sterowniki|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Biblioteka Intel MPI||
|Interfejs wiersza polecenia platformy Azure ||
|Przykłady Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|Środowiska **R** Tools & Environments|Szczegóły|
|----|:----:|
|RStudio Server Open Source Edition (wersja zapoznawcza)||
|Jądro języka R||
|Zestaw SDK Azure Machine Learning dla języka R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Przykłady zestawu SDK|

|Środowiska **Python** Tools & Environments|Szczegóły|
|----|----|
|Anaconda Python||
|Jupyter i rozszerzenia||
|Jupyterlab i rozszerzenia||
[Zestaw SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>z PyPI|Zawiera większość dodatkowych pakietów platformy Azure.  Aby wyświetlić pełną listę, [Otwórz okno terminalu na wystąpieniu obliczeniowym](how-to-run-jupyter-notebooks.md#terminal) i uruchom polecenie <br/> `conda list -n azureml_py36 azureml*` |
|Inne pakiety PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pakiety Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pakiety uczenia głębokiego|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pakiety ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Przykłady zestawu SDK języka Azure Machine Learning Python & R||

Wszystkie pakiety języka Python są zainstalowane w środowisku **python 3,6-Azure** .  

### <a name="installing-packages"></a>Instalowanie pakietów

Pakiety można instalować bezpośrednio w notesie Jupyter lub RStudio:

* RStudio Użyj karty **pakiety** w prawym dolnym rogu lub karty **konsoli** w lewym górnym rogu.  
* Python: Dodawanie kodu instalacji i wykonywanie w komórce notesu Jupyter.

Możesz też uzyskać dostęp do okna terminalu w dowolny z następujących sposobów:

* RStudio: Wybierz kartę **terminalu** u góry po lewej stronie.
* Jupyter Lab: wybierz kafelek **terminalu** pod **innym** nagłówkiem na karcie Uruchamianie.
* Jupyter: wybierz pozycję **Nowy Terminal>** w prawym górnym rogu na karcie pliki.
* SSH z maszyną.  Następnie zainstaluj pakiety Python w środowisku **python 3,6-Azure** .  Zainstaluj pakiety języka R w środowisku **języka r** .

## <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Notesy i skrypty języka R są przechowywane na domyślnym koncie magazynu Twojego obszaru roboczego w udziale plików platformy Azure.  Te pliki znajdują się w katalogu "pliki użytkownika". Ten magazyn ułatwia udostępnianie notesów między wystąpieniami obliczeniowymi. Konto magazynu umożliwia również bezpieczne zachowywanie Twoich notesów po zatrzymaniu lub usunięciu wystąpienia obliczeniowego.

Konto udziału plików platformy Azure w obszarze roboczym jest zainstalowane jako dysk w wystąpieniu obliczeniowym. Ten dysk jest domyślnym katalogiem roboczym dla Jupyter, Jupyter Labs i RStudio. Oznacza to, że Notesy i inne pliki tworzone w Jupyter, JupyterLab lub RStudio są automatycznie przechowywane w udziale plików i dostępne do użycia również w innych wystąpieniach obliczeniowych.

Pliki w udziale plików są dostępne we wszystkich wystąpieniach obliczeniowych w tym samym obszarze roboczym. Wszelkie zmiany tych plików w wystąpieniu obliczeniowym będą niezawodnie utrwalane w udziale plików.

Najnowsze przykłady Azure Machine Learning można także klonować do folderu w katalogu plików użytkownika w udziale plików obszaru roboczego.

Pisanie małych plików może być wolniejsze na dyskach sieciowych niż zapis na dysku lokalnym wystąpienia obliczeniowego.  Jeśli piszesz wiele małych plików, spróbuj użyć katalogu bezpośrednio w wystąpieniu obliczeniowym, takim jak `/tmp` katalog. Pamiętaj, że te pliki nie będą dostępne z innych wystąpień obliczeniowych. 

Możesz użyć `/tmp` katalogu w wystąpieniu obliczeniowym dla danych tymczasowych.  Nie należy jednak zapisywać dużych plików danych na dysku systemu operacyjnego wystąpienia obliczeniowego.  Zamiast nich należy używać [magazynów](concept-azure-machine-learning-architecture.md#datasets-and-datastores) danych. Jeśli zainstalowano rozszerzenie git JupyterLab, może to również prowadzić do spowolnienia wydajności wystąpienia obliczeniowego.

## <a name="managing-a-compute-instance"></a>Zarządzanie wystąpieniem obliczeniowym

W obszarze roboczym programu Azure Machine Learning Studio wybierz pozycję **obliczenia**, a następnie na górze wybierz pozycję **wystąpienie obliczeniowe** .

![Zarządzanie wystąpieniem obliczeniowym](./media/concept-compute-instance/manage-compute-instance.png)

Można wykonać następujące czynności:

* [Utwórz wystąpienie obliczeniowe](#create). 
* Odśwież kartę wystąpienia obliczeniowe.
* Uruchamianie, zatrzymywanie i ponowne uruchamianie wystąpienia obliczeniowego.  Płatność za wystąpienie jest dokonywana za każdym razem, gdy jest ono uruchomione. Zatrzymaj wystąpienie obliczeniowe, gdy nie jest używane, aby obniżyć koszty. Zatrzymywanie wystąpienia obliczeniowego powoduje jego przydział. Następnie uruchom ją ponownie, gdy będzie potrzebna. 
* Usuń wystąpienie obliczeniowe.
* Przefiltruj listę wystąpień obliczeniowych do utworzonych przez siebie.  Są to wystąpienia obliczeniowe, do których można uzyskać dostęp.

Dla każdego wystąpienia obliczeniowego w obszarze roboczym, do którego masz dostęp, możesz:

* Dostęp do Jupyter, JupyterLab, RStudio w wystąpieniu obliczeniowym
* Użyj protokołu SSH do wystąpienia obliczeniowego. Dostęp SSH jest domyślnie wyłączony, ale można go włączyć podczas tworzenia wystąpienia obliczeniowego. Dostęp SSH odbywa się za pośrednictwem mechanizmu publicznego/prywatnego klucza. Karta przekaże szczegóły dotyczące połączenia SSH, takie jak adres IP, nazwa użytkownika i numer portu.
* Pobierz szczegóły dotyczące określonego wystąpienia obliczeniowego, takiego jak adres IP i region.

[RBAC](/azure/role-based-access-control/overview) pozwala kontrolować, którzy użytkownicy w obszarze roboczym mogą tworzyć, usuwać, uruchamiać, zatrzymywać, ponownie uruchamiać wystąpienie obliczeniowe. Wszyscy użytkownicy z rolą współautor i właściciel obszaru roboczego mogą tworzyć, usuwać, uruchamiać, zatrzymywać i ponownie uruchamiać wystąpienia obliczeniowe w obszarze roboczym. Jednak tylko twórca określonego wystąpienia obliczeniowego może uzyskać dostęp do Jupyter, JupyterLab i RStudio na tym wystąpieniu obliczeniowym. Twórca wystąpienia obliczeniowego ma dla nich przypisane wystąpienie obliczeniowe, ma dostęp do katalogu głównego i może być terminalem za pomocą Jupyter/JupyterLab/RStudio. Wystąpienie obliczeniowe będzie miało jednokrotne logowanie użytkownika Creator, a wszystkie akcje będą używać tożsamości tego użytkownika do kontroli RBAC i naliczania przebiegów eksperymentu. Dostęp SSH jest kontrolowany za pośrednictwem mechanizmu publicznego/prywatnego klucza.

Te akcje można kontrolować za pomocą RBAC:
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/odczyt*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/zapis*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/usuwanie*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/uruchomienie/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/ponowne uruchomienie/akcja*

### <a name="create-a-compute-instance"></a><a name="create"></a>Tworzenie wystąpienia obliczeniowego

W obszarze roboczym programu Azure Machine Learning Studio Utwórz nowe wystąpienie obliczeniowe z sekcji **obliczenia** lub w sekcji **notesy** , gdy wszystko będzie gotowe do uruchomienia jednego z notesów.

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Tworzenie nowego wystąpienia obliczeniowego":::


|Pole  |Opis  |
|---------|---------|
|Nazwa obliczeniowa     |  <li>Nazwa jest wymagana i musi mieć długość od 3 do 24 znaków.</li><li>Prawidłowe znaki to wielkie i małe litery, cyfry i **-** znaki.</li><li>Nazwa musi rozpoczynać się od litery</li><li>Nazwa musi być unikatowa we wszystkich istniejących obliczeniach w regionie świadczenia usługi Azure. Jeśli wybrana nazwa nie jest unikatowa, zostanie wyświetlony alert</li><li>Jeśli **-** jest używany znak, musi następować co najmniej jedną literę w dalszej części nazwy</li>     |
|Typ maszyny wirtualnej |  Wybierz procesor CPU lub GPU. Tego typu nie można zmienić po utworzeniu     |
|Rozmiar maszyny wirtualnej     |  Obsługiwane rozmiary maszyn wirtualnych mogą być ograniczone w Twoim regionie. Sprawdź [listę dostępności](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Włącz/Wyłącz dostęp SSH     |   Dostęp SSH jest domyślnie wyłączony.  Nie można uzyskać dostępu do protokołu SSH. Zmieniono po utworzeniu. Upewnij się, że włączono dostęp, jeśli planujesz interaktywną debugowanie za pomocą usługi [vs Code Remote](how-to-set-up-vs-code-remote.md)   |
|Ustawienia zaawansowane     |  Opcjonalny. Skonfiguruj sieć wirtualną. Określ **grupę zasobów**, **sieć wirtualną**i **podsieć** , aby utworzyć wystąpienie obliczeniowe wewnątrz Virtual Network platformy Azure. Aby uzyskać więcej informacji, zobacz te [wymagania sieciowe dotyczące sieci](how-to-enable-virtual-network.md#compute-instance) wirtualnej.        |

Można również utworzyć wystąpienie
* Bezpośrednio w [środowisku zintegrowanych notesów](tutorial-1st-experiment-sdk-setup.md#azure)
* W Azure Portal
* Z szablonu Azure Resource Manager. Przykładowy szablon można znaleźć w temacie [Create a Azure Machine Learning COMPUTE instance Template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Z [zestawem SDK Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb)
* Z [rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

Dedykowane rdzenie na region według przydziału rodziny maszyn wirtualnych i łączny limit przydziału regionalnego, który ma zastosowanie do tworzenia wystąpienia obliczeniowego. jest ujednolicony i udostępniany przy użyciu Azure Machine Learning szkolenia dotyczące klastrów obliczeniowych. Zatrzymanie wystąpienia obliczeniowego nie powoduje zwolnienia przydziału w celu zapewnienia, że będzie można ponownie uruchomić wystąpienie obliczeniowe.

## <a name="compute-target"></a>Docelowy zasób obliczeniowy

Wystąpienia obliczeniowe mogą służyć jako [obiekt docelowy obliczeń szkoleniowych](concept-compute-target.md#train) podobny do klastrów szkoleniowych Azure Machine Learning COMPUTE. 

Wystąpienie obliczeniowe:
* Ma kolejkę zadań.
* Bezpieczne uruchamianie zadań w środowisku sieci wirtualnej, bez konieczności otwierania portu SSH przez przedsiębiorstwa. Zadanie jest wykonywane w środowisku kontenerowym i pakuje zależności modelu w kontenerze platformy Docker.
* Może uruchamiać wiele małych zadań równolegle (wersja zapoznawcza).  Dwa zadania na rdzeń mogą działać równolegle, podczas gdy pozostałe zadania są umieszczane w kolejce.

Wystąpienia obliczeniowego można użyć jako lokalnego celu wdrożenia inferencing na potrzeby scenariuszy testowania/debugowania.

> [!NOTE]
> Zadania szkoleniowe rozproszone nie są obsługiwane w wystąpieniu obliczeniowym.  Użyj (klastrów obliczeniowych) (instrukcje dotyczące konfiguracji-szkolenia-targets. MD # amlcompute) dla szkolenia rozproszonego.

Aby uzyskać więcej informacji, zobacz [szkolenie dotyczące notesu w computeinstance](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb). Ten Notes jest również dostępny w folderze Studio **Samples** *(szkolenia/uczenie na computeinstance*).

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Co się stało z maszyną wirtualną notesu?

Wystąpienia obliczeniowe zamieniają maszynę wirtualną notesu.  

Wszystkie pliki notesu przechowywane w udziale plików obszaru roboczego i dane w magazynach danych obszaru roboczego będą dostępne z wystąpienia obliczeniowego. Jednak wszystkie pakiety niestandardowe zainstalowane wcześniej na maszynie wirtualnej notesu będą musiały zostać ponownie zainstalowane w wystąpieniu obliczeniowym. Ograniczenia przydziałów, które dotyczą tworzenia klastrów obliczeniowych, będą również stosowane do tworzenia wystąpień obliczeniowych.

Nie można utworzyć nowych maszyn wirtualnych notesu. Można jednak nadal uzyskiwać dostęp do utworzonych maszyn wirtualnych Notes i korzystać z nich z pełną funkcjonalnością. Wystąpienia obliczeniowe można tworzyć w tym samym obszarze roboczym, co istniejące maszyny wirtualne notesu.


## <a name="next-steps"></a>Następne kroki

 * [Samouczek: uczenie swojego pierwszego modelu ml](tutorial-1st-experiment-sdk-train.md) pokazuje, jak używać wystąpienia obliczeniowego z zintegrowanym notesem.
