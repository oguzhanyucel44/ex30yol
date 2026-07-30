# EX30 Yol Analizi

Volvo EX30 (Android Automotive OS) için yolculuk analiz uygulaması. Her yolculuğu
kendiliğinden kaydeder; gerçek tüketimi, gösterge menzilinin sapmasını, rakım
kaynaklı enerji akışını ve hızlanma ölçümlerini hesaplar. Sürüş sırasında
kullanıcı hiçbir şeye dokunmaz.

- Paket: `com.oguzhanyucel.ex30journey`
- Modül: tek modül `:automotive`
- Kategori: `NAVIGATION` + `NavigationTemplate`
- Dağıtım: Play Console Dahili Test
- İnternet izni **yok** — hiçbir veri cihaz dışına çıkmaz.

## Derleme

`java`, `gradle` ve `adb` PATH'te değil; her oturumda:

```bash
export JAVA_HOME="C:/Program Files/Android/Android Studio/jbr"
```

```bash
./gradlew :automotive:assembleDebug
```

Yayın paketi:

```bash
./gradlew :automotive:bundleRelease
```

## Emülatörde çalıştırma

Sürücü profili **user 0 değil, user 10**.

```bash
adb install -r -t automotive/build/outputs/apk/debug/automotive-debug.apk
adb shell cmd location set-location-enabled true --user 10
adb shell am start --user 10 -n "com.oguzhanyucel.ex30journey/androidx.car.app.activity.CarAppActivity"
```

İzinler: `CAR_SPEED` ve `CAR_ENERGY` bu imajda **çalışma anı** izni ve
verilmezse araç verisi hiç gelmez (belirtisi: property'ler `getPropertyList()`
çıktısında görünmez). Uygulama açılışta ister; elle vermek için:

```bash
adb shell pm grant --user 10 com.oguzhanyucel.ex30journey android.permission.ACCESS_FINE_LOCATION
adb shell pm grant --user 10 com.oguzhanyucel.ex30journey android.car.permission.CAR_SPEED
adb shell pm grant --user 10 com.oguzhanyucel.ex30journey android.car.permission.CAR_ENERGY
```

## Debug kancası

Host, dokunuşları uygulamaya iletmediği ve `input tap` düğmelere isabet
etmediği için emülatörde ekranlar broadcast ile açılır:

```bash
adb shell am broadcast --user 10 -p com.oguzhanyucel.ex30journey -a com.oguzhanyucel.ex30journey.DEBUG --es cmd screen --es to calib
```

`to` değerleri: `calib`, `trips`, `records`, `live`. Kanca yalnızca debug
derlemesinde kurulur.

Kalibrasyon kaydını okumak:

```bash
adb shell run-as com.oguzhanyucel.ex30journey --user 10 tail -20 files/calib.csv
```

Uygulama çalışırken APK değiştirmek host'u çökertir; temiz yol için önce
`am force-stop`, kurulumdan sonra template host'unu da yeniden başlat.

Ekran görüntüsü ekran kimliği ister:

```bash
adb shell screencap -p -d 4619827259835644672 /sdcard/shot.png
```

## Klasörler

| Yol | İçerik |
|---|---|
| `automotive/src/main/java/.../car/` | Araç property'lerine erişim, `VehicleDataHub` |
| `automotive/src/main/java/.../loc/` | `LocationManager` sarmalayıcı |
| `automotive/src/main/java/.../trip/` | Yolculuk durum makinesi, kalıcılık, metrikler |
| `automotive/src/main/java/.../screen/` | Car App Library ekranları |
| `automotive/src/main/java/.../render/` | Surface çizimi |
| `automotive/src/main/java/.../debug/` | Yalnızca debug derlemesindeki broadcast kancası |
| `automotive/src/main/java/.../calib/` | Faz 0 kalibrasyon sondası |
| `privacy/` | Gizlilik politikası sayfası (GitHub Pages'e yüklenir) |
| `tools/` | Emülatör betikleri |

## Belgeler

- `versiyon.md` — sürüm günlüğü, sade dil
- `PLAY-CONSOLE.md` — mağaza kaydı alanları
- `EX30-YOL-ANALIZI-PROMPT.md` — proje tanımı
