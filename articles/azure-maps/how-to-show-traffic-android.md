---
title: Wyświetlanie danych o ruchu na mapie systemu Android | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlać dane o ruchu na mapie przy użyciu Android SDK Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 73f490ec069ff44929ca70f4ecf2ab3aca52934d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209352"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Wyświetlanie danych o ruchu na mapie przy użyciu Azure Maps Android SDK

Dane przepływu i dane zdarzeń to dwa typy danych ruchu, które mogą być wyświetlane na mapie. W tym przewodniku pokazano, jak wyświetlić oba te dane o ruchu. Dane zdarzeń obejmują dane dotyczące punktów i danych opartych na wierszach dla elementów, takich jak konstrukcje, zamknięcia dróg i awarie. Dane przepływu przedstawiają metryki dotyczące przepływu ruchu w podróży.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wyświetlić ruch na mapie, należy zainstalować [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) i załadować mapę.

## <a name="incidents-traffic-data"></a>Zdarzenia danych ruchu 

Aby wywołać `setTraffic` i `incidents`, należy zaimportować następujące biblioteki:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Poniższy fragment kodu przedstawia sposób wyświetlania danych o ruchu na mapie. Przekazujemy wartość logiczną do metody `incidents` i przekazujemy ją do metody `setTraffic`. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
}
}
```

## <a name="flow-traffic-data"></a>Dane ruchu przepływu

Najpierw należy zaimportować następujące biblioteki, aby wywołać `setTraffic` i `flow`:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Użyj poniższego fragmentu kodu, aby ustawić dane przepływu ruchu. Podobnie jak w przypadku kodu w poprzedniej sekcji, przekazujemy wartość zwracaną metody `flow` do metody `setTraffic`. Istnieją cztery wartości, które można przekazywać do `flow`, a każda wartość wywoła `flow`, aby zwrócić odpowiednią wartość. Wartość zwracana `flow` zostanie następnie przeniesiona jako argument do `setTraffic`. Zapoznaj się z poniższą tabelą dla tych czterech wartości:

| | |
| :-- | :-- |
| TrafficFlow. NONE | Nie wyświetla danych o ruchu na mapie |
| TrafficFlow. RELATYWN | Pokazuje dane o ruchu odnoszące się do szybkości swobodnego przepływu drogi |
| TrafficFlow. RELATIVE_DELAY | Wyświetla obszary, które są wolniejsze niż średnie oczekiwane opóźnienie |
| TrafficFlow. ABSOLUTN | Pokazuje absolutną prędkość wszystkich pojazdów w podróży |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> 
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Pokaż dane o ruchu zdarzeń, klikając funkcję

Aby uzyskać zdarzenia dotyczące konkretnej funkcji, można użyć poniższego kodu. Po kliknięciu funkcji logika kodu sprawdza zdarzenia i kompiluje komunikat o zdarzeniu. W dolnej części ekranu zostanie wyświetlony komunikat z informacjami.

1. Najpierw należy edytować **> zasobów > activity_main. XML**, tak aby wyglądał wyglądać podobnie do przedstawionego poniżej. Możesz zastąpić `mapcontrol_centerLat`, `mapcontrol_centerLng`i `mapcontrol_zoom` odpowiednimi wartościami. Odwołaj, poziom powiększenia jest wartością z zakresu od 0 do 22. Na poziomie powiększenia 0 cały świat mieści się na jednym kafelku.

   ```XML
   <?xml version="1.0" encoding="utf-8"?>
   <FrameLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       >
    
       <com.microsoft.azure.maps.mapcontrol.MapControl
           android:id="@+id/mapcontrol"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Dodaj następujący kod do pliku **Main. Java** . Pakiet jest domyślnie dołączany, więc upewnij się, że pakiet znajduje się u góry.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
       }

       @Override
       public void onPause() {
           super.onPause();
           mapControl.onPause();
       }

       @Override
       public void onStop() {
           super.onStop();
           mapControl.onStop();
       }

       @Override
       public void onLowMemory() {
           super.onLowMemory();
           mapControl.onLowMemory();
       }

       @Override
       protected void onDestroy() {
           super.onDestroy();
           mapControl.onDestroy();
       }

       @Override
       protected void onSaveInstanceState(Bundle outState) {
           super.onSaveInstanceState(outState);
           mapControl.onSaveInstanceState(outState);
       }
   }
   ```

3. Po dodaniu powyższego kodu do aplikacji będzie można kliknąć funkcję i zobaczyć szczegóły zdarzeń dotyczących ruchu sieciowego. W zależności od szerokości geograficznej, długości geograficznej oraz wartości poziomu powiększenia, które były używane w pliku **activity_main. XML** , wyniki wyglądają podobnie jak na poniższym obrazie:

   <center>

   ![Zdarzenia — ruch przychodzący na mapie](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dodać więcej danych do mapy, zobacz następujące przewodniki:

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę kafelków](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Informacje o funkcji wyświetlania](display-feature-information-android.md)
