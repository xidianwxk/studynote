```
model [states: TYPE+ ,R: [states -> states]] % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
  % assuming declarations
  % ENDASSUMING

    property: type = [states -> bool]    %V:某一状态下，命题成立与否

    p, p1 ,p2: var property
    s, s1, s2: var states

    true: property = lambda (s): true     %这里lambda (s)等价于λs
    false: property = lambda (s): false

    nega: [property -> property] = lambda (p): (lambda(s): not p(s))
    con: [property , property -> property] = lambda (p1,p2): (lambda(s): p1(s) and p2(s))
    dis: [property , property -> property] = lambda (p1,p2): (lambda(s): p1(s) or p2(s))
    impl: [property , property -> property] = lambda (p1,p2): (lambda(s): (not p1(s) or p2(s)))
    
    G: [property -> property] = lambda(p): (lambda(s): forall s1: R(s)=s1 =>p(s1))
    F: [property -> property] = lambda(p): (lambda(s): exists s1: R(s)=s1 and p(s1))
    X: [property -> property] = lambda(p): (lambda(s): (exists s1: (p(s1) and R(s)=s1 and s ≠ s1 and
         (not(exists s2: (R(s)=s2 and R(s2)=s1 and s ≠ s2 and s2 ≠ s1))))))

    U: [property , property -> property] = lambda(p1,p2): (lambda(s): exists(s1): (p2(s1) and R(s)=s1 and
         (forall s2: ((R(s)=s2 and R(s2)=s1 and s2 ≠ s1) => p1(s2)))))


   %about axioms
   
   R_refl	: axiom forall s: R(s)=s
   R_trans	: axiom forall s,s1,s2: R(s)=s1 and R(s1)=s2 => R(s)=s2

   %about lemmas

   G_and	: lemma impl(G(con(p1,p2)), (con(G(p1),G(p2))))=true
   G_X_swap : lemma X(G(p)) = G(X(p))
   G_imp_X  : lemma G(p)(s) => F(p)(s)

  END model
```

