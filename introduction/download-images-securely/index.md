---
title: Kali Linux 이미지 안전하게 다운로드하기
description:
icon:
weight: 51
author: ["daniruiz",]
번역: ["xenix4845"]
---

이미지를 다운로드할 때는 다운로드한 이미지 옆에 있는(즉, [Kali Linux 다운로드 서버](http://cdimage.kali.org/)의 동일한 디렉토리에 있는) **SHA256SUMS** 및 **SHA256SUMS.gpg** 파일도 함께 다운로드하세요. 이미지의 체크섬을 확인하기 전에, SHA256SUMS 파일이 Kali에서 생성한 것인지 확인해야 합니다. 이것이 바로 해당 파일이 Kali의 공식 키로 서명되어 SHA256SUMS.gpg에 분리된 서명으로 제공되는 이유입니다. Kali의 공식 키는 다음과 같이 다운로드할 수 있습니다:

<!--
```
$ wget -q -O - https://archive.kali.org/archive-key.asc | gpg --import
# or...
$ gpg --keyserver hkps://keys.openpgp.org --recv-key 827C8569F2518CC677FECA1AED65462EC8D5E4C5
# ...and verify that the displayed fingerprint matches the one below
$ gpg --fingerprint 827C8569F2518CC677FECA1AED65462EC8D5E4C5
pub   rsa4096 2025-04-17 [SC] [expires: 2028-04-17]
      827C 8569 F251 8CC6 77FE  CA1A ED65 462E C8D5 E4C5
uid           [ unknown] Kali Linux Archive Automatic Signing Key (2025) <devel@kali.org>
```

Color highlighted with "Copy as HTML" from gnome-terminal
-->
<pre><code class="nohighlight"><!-- New link hack
--><font color="#367BF0">$</font> <font color="#5EBDAB">wget</font> <font color="#9755B3">-q</font> <font color="#9755B3">-O</font> <font color="#9755B3">-</font> <font color="#2777ff">https://archive.kali.org/archive-key.asc</font> <font color="#277FFF"><b>|</b></font> <font color="#5EBDAB">gpg</font> <font color="#9755B3">--import</font>
# or...
<font color="#367BF0">$</font> <font color="#5EBDAB">gpg</font> <font color="#9755B3">--keyserver</font> <font color="#2777ff">hkps://keyserver.ubuntu.com</font> <font color="#9755B3">--recv-key</font> 827C8569F2518CC677FECA1AED65462EC8D5E4C5
# ...and verify that the displayed fingerprint matches the one below
<font color="#367BF0">$</font> <font color="#5EBDAB">gpg</font> <font color="#9755B3">--fingerprint</font> 827C8569F2518CC677FECA1AED65462EC8D5E4C5
pub   rsa4096 2025-04-17 [SC] [expires: 2028-04-17]
      827C 8569 F251 8CC6 77FE  CA1A ED65 462E C8D5 E4C5
uid           [ unknown] Kali Linux Archive Automatic Signing Key (2025) <devel@kali.org>
</code></pre>

**SHA256SUMS**와 **SHA256SUMS.gpg**를 모두 다운로드한 후, 다음과 같이 서명을 검증할 수 있습니다:

<!--
```
$ wget -q https://cdimage.kali.org/current/SHA256SUMS{.gpg,}
$ gpg --verify SHA256SUMS.gpg SHA256SUMS
gpg: Signature made Sun 20 Apr 2025 16:00:00 GMT
gpg:                using RSA key 827C8569F2518CC677FECA1AED65462EC8D5E4C5
gpg: Good signature from "Kali Linux Archive Automatic Signing Key (2025) <devel@kali.org>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
```

Color highlighted with "Copy as HTML" from gnome-terminal
-->
<pre><code class="nohighlight"><!-- New link hack
--><font color="#367BF0">$</font> <font color="#5EBDAB">wget</font> <font color="#9755B3">-q</font> <font color="#2777ff">https://cdimage.kali.org/current/SHA256SUMS</font>{.gpg,}
<font color="#367BF0">$</font> <font color="#5EBDAB">gpg</font> <font color="#9755B3">--verify</font> SHA256SUMS.gpg SHA256SUMS
gpg: Signature made Sun 20 Apr 2025 16:00:00 GMT
gpg:                using RSA key 827C8569F2518CC677FECA1AED65462EC8D5E4C5
gpg: Good signature from "Kali Linux Archive Automatic Signing Key (2025) &lt;devel@kali.org&gt;" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
</code></pre>

"Good signature" 메시지를 받지 못하거나 키 ID가 일치하지 않는 경우, 과정을 중단하고 정상적인 Kali 미러에서 이미지를 다운로드했는지 확인해야 합니다.
