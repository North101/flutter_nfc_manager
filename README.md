# nfc_manager

A Flutter plugin to use NFC. Supported on both Android and iOS.

## Setup

### Android Setup

* Add [android.permission.NFC](https://developer.android.com/reference/android/Manifest.permission.html#NFC) to your `AndroidMenifest.xml`.

### iOS Setup

* Add [Near Field Communication Tag Reader Session Formats Entitlements](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_nfc_readersession_formats) to your entitlements.

* Add [NFCReaderUsageDescription](https://developer.apple.com/documentation/bundleresources/information_property_list/nfcreaderusagedescription) to your `Info.plist`.

* Add [com.apple.developer.nfc.readersession.felica.systemcodes](https://developer.apple.com/documentation/bundleresources/information_property_list/systemcodes) and [com.apple.developer.nfc.readersession.iso7816.select-identifiers](https://developer.apple.com/documentation/bundleresources/information_property_list/select-identifiers) to your `Info.plist` as needed.

## Usage

### Managing Session

``` dart
// Start session and register callback.
NfcManager.instance.startTagSession(
  alertMessageIOS: '...',
  pollingOptions: {TagPollingOption.iso14443, TagPollingOption.iso15693, TagPollingOption.iso18092},
  onDiscovered: (NfcTag tag) {
    // Manipulating tag
  },
);

// Stop session and unregister callback.
NfcManager.instance.stopSession(
  alertMessageIOS: '...',
  errorMessageIOS: '...',
);
```

### Manipulating NDEF

``` dart
// Obtain an Ndef instance from tag
Ndef ndef = Ndef.fromTag(tag);

if (ndef == null) {
  print('Tag is not ndef');
  return;
}

// Read an NdefMessage object cached at discovery time
print(ndef.cachedMessage);

if (!ndef.isWritable) {
  print('Tag is not ndef writable');
  return;
}

NdefMessage messageToWrite = NdefMessage([
  NdefRecord.createText('Hello'),
  NdefRecord.createUri(Uri.parse('https://flutter.dev')),
  NdefRecord.createMime('text/plain', Uint8List.fromList('Hello'.codeUnits)),
  NdefRecord.createExternal('mydomain', 'mytype', Uint8List.fromList('mydata'.codeUnits)),
]);

// Write an NdefMessage
try {
  await ndef.write(messageToWrite);
} catch (e) {
  // handle error
  return;
}
```

### Manipulating Platform-Specific-Tag

The following platform-specific-tag classes are available:

**iOS**

* MiFare
* FeliCa
* ISO15693
* ISO7816

**Android**

* NfcA
* NfcB
* NfcF
* NfcV
* IsoDep

**Example**

``` dart
MiFare miFare = MiFare.fromTag(tag);

if (miFare == null) {
  print('MiFare is not available on this tag');
  return;
}

Uint8List response = await miFare.sendMiFareCommand(...);
```
