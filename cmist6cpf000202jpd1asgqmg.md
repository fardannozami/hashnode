---
title: "portfolio fardan nozami"
datePublished: Fri Dec 05 2025 11:55:50 GMT+0000 (Coordinated Universal Time)
cuid: cmist6cpf000202jpd1asgqmg
slug: portfolio-fardan-nozami
tags: curhat

---

Oke Fardan, tinggal kasih **flex + justify-center + text-center** di container tulisan itu. Quick fix yang paling clean gini aja 👇

```tsx
export function Footer() {
  return (
    <footer className="py-8 border-t border-border">
      <div className="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
        <div className="flex items-center justify-center text-center">
          <p className="text-sm text-muted-foreground">
            © {new Date().getFullYear()} Fardan Nozami Ajitama. All rights reserved.
          </p>
        </div>
      </div>
    </footer>
  )
}
```

**Hasilnya:** teks auto center secara horizontal, dan kalau mau tetap responsif nggak perlu flex-column layout di sini.

Kalau kamu mau versi tetap pakai layout awal (flex-col di mobile, row di desktop) tapi teks tetap center, tinggal gini:

```tsx
<div className="flex flex-col sm:flex-row items-center justify-center text-center gap-4">
```

— Tambahin **justify-center** + **text-center** aja.

Mau sekalian dibikinin versi yang lebih aesthetic + subtle animation?