---
title: "Cloudflare 移管後に GitHub Pages が壊れた"
date: 2023-12-26T20:57:50+09:00
draft: true
---

Google Domains が Squarespace に売却されたので、kamasaki.net を Cloudflare に移管した。
その際 MX レコードには特に問題がなかったが、GitHub Pages で構築したこのブログにアクセスできなくなっていた。

# 解決法

kmn4.github.io に向けていた CNAME レコードの proxy status を DNS only にすることで、無事 HTTPS アクセスができるようになった。

# 原因と思しきもの

https://github.com/orgs/community/discussions/23632#discussioncomment-3241245
https://developers.cloudflare.com/dns/manage-dns-records/reference/proxied-dns-records/
紆余曲折あったため十分に検証できていないが、恐らくこの問題が関係している。
Cloudflare の DNS レコード設定には "Proxy status" という項目があって、これが "Proxied" だと、dig ったときに Cloudflare の IP アドレスが返ってくる[^anycast-ip-list]。
これにより GitHub Pages への HTTP(S) トラフィックは必ず Cloudflare を通ることとなり、Cloudflare 側でキャシュや DDoS 対策ができるらしいが、それと同時に GitHub 側が HTTPS 用のサーバ証明書を更新できなくなるようだ[^ACME-problem][^http-might-have-succeded]。

[^anycast-ip-list]: Cloudflare の IP アドレスレンジはこちら: https://www.cloudflare.com/ips/
[^http-might-have-succeded]: この理屈なら DNS レコード修正前でも HTTP ではアクセスできたはずだが、修正前に試してないためなんとも言えない。
[^ACME-problem]: ACME プロトコルによるドメイン検証ができなくなるということだろうが、詳しくないので説明できない。
