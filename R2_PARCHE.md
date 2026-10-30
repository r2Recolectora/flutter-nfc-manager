# nfc_manager 4.2.1 con parche r2

Fork de [nfc_manager 4.2.1](https://pub.dev/packages/nfc_manager/versions/4.2.1)
con un solo cambio en
`android/src/main/kotlin/dev/flutter/plugins/nfcmanager/NfcManagerPlugin.kt`
(`refreshAdapter()`, `nfcAdapterIsEnabled()` y `getAdapter()`).

## El problema

Al encender el teléfono, Android puede levantar el proceso de la app (por
ejemplo, HyperOS relanza la última app abierta) antes de que el servicio NFC del
sistema esté listo. El
plugin original pide el adaptador una sola vez, en `onAttachedToEngine`, con el
`applicationContext`; en ese momento Android devuelve `null` y deja ese
`NfcManager` cacheado en el Context mientras viva el proceso. Resultado: la app
muestra "Activa el NFC en Ajustes" con el NFC prendido, hasta que se cierra a la
fuerza. Visto en el Xiaomi 13T Pro el 2026-10-30; reproducido reiniciando el
teléfono con la app abierta (adaptador `null` al arrancar, NFC listo 2 s después).

## El parche

Si el adaptador es `null` o reporta NFC apagado, se vuelve a pedir con un
Context nuevo (`createConfigurationContext`), que tiene su propia caché de
servicios. Si el NFC de verdad está apagado, sigue respondiendo "apagado".

## Al actualizar el plugin

Revisar si la versión nueva ya vuelve a pedir el adaptador; si no, copiar la
versión nueva aquí y reaplicar el parche.
