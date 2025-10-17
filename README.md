## Easiest path: use the single-file build

Copy only sodium.js to public/assets/crypto/.

Load it client-side; there will be no network request for .wasm (that’s normal):

```tsx
"use client";
import { useEffect, useState } from "react";

export default function SodiumDemo() {
  const [ok, setOk] = useState("…");

  useEffect(() => {
    (async () => {
      await new Promise<void>((resolve, reject) => {
        const s = document.createElement("script");
        s.src = "/assets/crypto/sodium.js";  // single file
        s.async = true;
        s.onload = () => resolve();
        s.onerror = reject;
        document.head.appendChild(s);
      });

      // @ts-ignore set by the loader
      const sodium = (window as any).sodium;
      if (sodium?.ready) await sodium.ready;

      const key = sodium.crypto_aead_xchacha20poly1305_ietf_keygen();
      setOk(key?.length === 32 ? "OK" : "FAIL");
    })().catch(() => setOk("FAIL"));
  }, []);

  return <pre>libsodium (single-file): {ok}</pre>;
}
```
