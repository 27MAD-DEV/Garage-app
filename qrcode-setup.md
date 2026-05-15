# QR Code Scanner Setup Guide - Garage App

## Current Status ✅
Your app is already using **html5-qrcode v2.3.8** library, which is a reliable, production-ready QR code scanner.

## What's Working
- QR code scanner UI interface (lines 511-588 in index.html)
- Camera integration with start/stop controls
- Manual input fallback for barcode numbers
- Scanner camera switching capability

## Troubleshooting Your QR Code Reading Issues

### Problem: QR codes not reading properly

#### Solution 1: Improve QR Code Quality
The QR code you showed has corrupted data bits. For proper scanning:

1. **Generate High-Quality QR Codes**
   - Use reliable generators: [qr-code-generator.com](https://www.qr-code-generator.com) or [goqr.me](https://goqr.me)
   - Use error correction level: **High (H)** - can recover even if 30% is damaged
   - Use solid black and white (no gradients)

2. **Optimal QR Code Settings**
   ```
   - Size: At least 100x100 pixels
   - Color: Pure black (#000000) on pure white (#FFFFFF)
   - Error Correction: Level H (30% recovery)
   - Clear borders: 4 modules minimum
   ```

#### Solution 2: Camera Optimization
```javascript
// Add this to improve camera scanning in your JavaScript:

const qrScanner = new Html5Qrcode(
    "scanner-container",
    {
        facingMode: "environment",
        qrbox: {
            width: 250,
            height: 250
        },
        aspectRatio: 1.33,
        showTorchButtonIfSupported: true,
        videoConstraints: {
            facingMode: { ideal: "environment" },
            width: { min: 720, ideal: 1280 },
            height: { min: 720, ideal: 720 }
        }
    }
);
```

#### Solution 3: Better Success/Error Handling
```javascript
function onScanSuccess(decodedText, decodedResult) {
    console.log("✅ QR Code detected:", decodedText);
    // Your processing logic here
    playSound('sound-click');
    processManualEntry(decodedText);
}

function onScanFailure(error) {
    // Ignore this error - scanning continuously
    // Only log camera errors, not reading failures
    if (error.includes("NotAllowedError")) {
        console.error("❌ Camera permission denied");
    }
}
```

### Problem: Camera Permission Denied

**Browser Requirements:**
- HTTPS only (except localhost)
- User permission grant
- Modern browser (Chrome 60+, Firefox 55+, Safari 13+)

**Fix:**
```javascript
// Check permissions before starting scanner
async function startScanner() {
    try {
        const permission = await navigator.permissions.query({ name: 'camera' });
        if (permission.state === 'denied') {
            showToast('Camera access denied. Enable it in browser settings.', 'error');
            return;
        }
        // Start scanner...
    } catch (error) {
        console.error("Camera access error:", error);
    }
}
```

### Problem: Slow Scanner Performance

**Optimization Tips:**
1. **Reduce processing frequency:**
   ```javascript
   const fps = 10; // Scan 10 times per second instead of continuous
   Html5Qrcode.DEFAULT_QR_BOX_SIZE = 250;
   ```

2. **Use lighter constraints:**
   - Start with 720p instead of 4K
   - Adjust `qrbox` to match actual QR code size

3. **Test in console:**
   ```javascript
   // Open DevTools (F12) → Console → paste this:
   console.log("Scanner active:", Html5Qrcode.isSupported());
   ```

## Enhanced QR Code Generation for Your App

To generate QR codes for your card system, add this to your JavaScript:

```javascript
// Generate QR codes for cards
async function generateCardQRCode(cardNumber) {
    // Using QR Server API (free, no installation needed)
    const qrUrl = `https://api.qrserver.com/v1/create-qr-code/?size=300x300&data=${encodeURIComponent(cardNumber)}&ecc=H`;
    return qrUrl;
}

// Example usage:
const card1QR = await generateCardQRCode('CARD-001');
console.log(card1QR); // Print to use
```

## Testing Checklist

- [ ] Open app in HTTPS (or localhost)
- [ ] Click "تشغيل الكاميرا" (Start Camera)
- [ ] Grant camera permission
- [ ] Point camera at high-quality QR code
- [ ] Check browser console (F12) for errors
- [ ] Verify scanner frame appears around QR
- [ ] Test manual input as fallback

## Resources

- **html5-qrcode Docs**: [github.com/mebjas/html5-qrcode](https://github.com/mebjas/html5-qrcode)
- **QR Code Best Practices**: [QR Code Specifications](https://www.qr-code.co.jp/en/about/standards.html)
- **Testing Tools**: [ZXing Decoder Online](https://zxing.org/w/decode.jspx)

## Next Steps

1. ✅ **Verify HTTPS deployment** - QR scanning requires secure context
2. ✅ **Test with high-quality QR codes** - Generate with error correction level H
3. ✅ **Monitor console logs** - Use F12 DevTools to debug
4. ✅ **Check camera permissions** - Ensure browser has camera access
5. ✅ **Use fallback input** - Always have manual entry option available

## Quick Debug Commands

Run these in browser console (F12):
```javascript
// Check if QR scanning is supported
Html5Qrcode.isSupported().then(supported => console.log("QR scanning supported:", supported));

// Check camera availability
navigator.mediaDevices.enumerateDevices().then(devices => 
    console.log("Cameras:", devices.filter(d => d.kind === 'videoinput'))
);

// Test QR decoder
const testCode = "CARD-12345";
console.log("Test encode:", btoa(testCode));
```

---

**Need help?** Check the browser console (F12) for specific error messages and share them for targeted support.
