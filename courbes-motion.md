# Courbes de motion à donner à Claude

Colle ce fichier dans ta conversation avec Claude (ou dans le skill de ton projet) avant de lui demander une animation.

## Le problème

Le skill HyperFrames donne en exemple : `duration: 0.6`, `ease: "power3.out"`, `y: 24`, décalage `0.08`.
Recopiées telles quelles, ces valeurs font tout bouger pareil.

## Les courbes (cubic-bezier)

| Nom | Valeur | Usage |
|---|---|---|
| `entree` | `.8, 0, .4, 1` | entrées de blocs, cartes, titres, captures. 0,7 à 0,8 s. Départ très doux (80 %), arrivée douce (60 %). |
| `sortie` | `.6, 0, .9, .4` | tout ce qui sort. Environ 0,45 s : presque deux fois plus vite qu'à l'entrée. |
| `impact` (optionnel) | `.08, .85, .15, 1` | un mot ou un chiffre qui claque. 0,45 s. Départ sec, arrêt net. |

## Les règles

1. Jamais `power3.out` partout. Une courbe par usage.
2. Une entrée dure 0,7 à 0,8 s. En dessous de 0,5 s, une courbe en S a l'air de démarrer en retard.
3. Une sortie est plus rapide que l'entrée correspondante.
4. Compteurs, rotations continues, défilements : `ease: "none"` (linéaire).
5. Décalage inégal entre les éléments : le principal en premier, les autres derrière. Pas un pas fixe de 0,08 s.
6. Pas de rebond (`back.out`) sauf effet volontairement ludique.

## À coller dans le code (GSAP)

```js
function bez(x1, y1, x2, y2) {
  var cx = 3 * x1, bx = 3 * (x2 - x1) - cx, ax = 1 - cx - bx,
      cy = 3 * y1, by = 3 * (y2 - y1) - cy, ay = 1 - cy - by;
  return function (x) {
    if (x <= 0) return 0; if (x >= 1) return 1; var t = x;
    for (var i = 0; i < 8; i++) {
      var d = (3 * ax * t + 2 * bx) * t + cx; if (Math.abs(d) < 1e-6) break;
      t -= (((ax * t + bx) * t + cx) * t - x) / d;
    }
    t = Math.min(1, Math.max(0, t));
    return ((ay * t + by) * t + cy) * t;
  };
}
gsap.registerEase("entree", bez(.8, 0, .4, 1));
gsap.registerEase("sortie", bez(.6, 0, .9, .4));
gsap.registerEase("impact", bez(.08, .85, .15, 1));

// exemple
gsap.fromTo(".carte", { y: 46, opacity: 0 }, { y: 0, opacity: 1, duration: 0.8, ease: "entree" });
gsap.to(".carte", { y: -30, opacity: 0, duration: 0.45, ease: "sortie" });
```

## Phrase à dire à Claude

« Utilise les courbes `entree`, `sortie` et `impact` du fichier, respecte les règles, et n'utilise jamais `power3.out` par défaut. »
