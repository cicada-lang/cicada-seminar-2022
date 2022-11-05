---
title: Scope problem of implicit argument and fulfilling class type
---

```cicada
class Isomorphism {
  cat: Category
  dom: cat.Object
  cod: cat.Object
  morphism: cat.Morphism(dom, cod)
  inverse: cat.Morphism(cod, dom)

  inverse_left: Equal(
    cat.Morphism(dom, dom),
    cat.compose(morphism, inverse),
    cat.id(dom),
  )

  inverse_right: Equal(
    cat.Morphism(cod, cod),
    cat.compose(inverse, morphism),
    cat.id(cod),
  )
}
```

问题叙述：

- 上面 `cat.compose` 是个 `PiImplicit`。
- 以 `inverse_left` 中的 `cat.compose(morphism, inverse)` 为例。
- elaboration 过程中会根据 `morphism` 的类型 insert `ApImplicit`，
  得到 `cat.compose(implicit x1, implicit x2, morphism, implicit x3, inverse)`。
- 解出来，得到 `cat.compose(implicit dom, implicit dom, morphism, implicit dom, inverse)`，
  虽然这么写，但是解不如带入到 core 里，
  而是在所形成的 closure 的 env 中，
  以 `x1 => dom` 的形式存在。
- 此时 `dom` 在 scope 中，但是只有类型 `dom: cat.Object`，没有值，是 neutral variable。
- 下面的 fulfilling class type `Isomorphism(cat, x.object, y.object)`
  会给出 `dom` 的值 `x.object`。
- 在 fulfilling class type 中，evaluate 到 `inverse_left` 的类型时候，
  scope 中有 `dom => x.object`，和 `x1 => dom`，
  evaluate variable `x1` 只能得到作为 neutral variable 的 `dom`。


```cicada
import { Isomorphism } from "../category/index.cic"
import { equal_swap, equal_compose } from "../equality/index.cic"

function terminal_object_isomorphism(
  cat: Category,
  x: Terminal(cat),
  y: Terminal(cat),
): Isomorphism(cat, x.object, y.object) {
  let f = x.morphism(y.object)
  let g = y.morphism(x.object)

  return {
    cat,
    dom: x.object,
    cod: y.object,
    morphism: y.morphism(x.object),
    inverse: x.morphism(y.object),

    inverse_left: equal_compose(
      x.morphism_unique(cat.compose(g, f)),
      equal_swap(x.morphism_unique(cat.id(x.object))),
    ),

    inverse_right: equal_compose(
      y.morphism_unique(cat.compose(f, g)),
      equal_swap(y.morphism_unique(cat.id(y.object))),
    ),
  }
}
```
