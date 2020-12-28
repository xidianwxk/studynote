### prelude



#### booleans

```
booleans: THEORY
 BEGIN

  boolean: NONEMPTY_TYPE
  bool: NONEMPTY_TYPE = boolean
  FALSE, TRUE: bool
  NOT, ¬: [bool -> bool]
  AND, &, ∧, OR, ∨, IMPLIES, =>, ⇒, WHEN, IFF, <=>, ⇔: [bool, bool -> bool]

 END booleans
```

#### equalities

```
equalities [T: TYPE]: THEORY
 BEGIN

  =: [T, T -> boolean]

 END equalities
 
 
notequal[T: TYPE]: THEORY
 BEGIN
  x, y: VAR T

  /=(x, y): boolean = NOT (x = y);
  ≠: [T, T -> bool] = /=
 END notequal
```

####if_def

```
if_def [T: TYPE]: THEORY
 BEGIN

  IF:[boolean, T, T -> T]

 END if_defif_def [T: TYPE]: THEORY
 BEGIN

  IF:[boolean, T, T -> T]

 END if_def
```

#### boolean_props

```
boolean_props: THEORY
 BEGIN
  A, B: VAR bool

  bool_exclusive: POSTULATE not (false = true)
  bool_inclusive: POSTULATE A = false or A = true

  not_def:       POSTULATE (not A) = IF A THEN false ELSE true ENDIF
  and_def:       POSTULATE (A and B) = IF A THEN B ELSE false ENDIF
  syand_def:     POSTULATE & = and
  or_def:        POSTULATE (A or B) = IF A THEN true ELSE B ENDIF
  implies_def:   POSTULATE (A implies B) = IF A THEN B ELSE true ENDIF
  syimplies_def: POSTULATE => = implies
  when_def:      POSTULATE (A when B) = (B implies A)
  iff_def:       POSTULATE (A iff B) = ((A and B) or (not A and not B))
  syiff_def:     POSTULATE <=> = iff

  excluded_middle: LEMMA A OR NOT A  

 END boolean_props
```

####xor_def

```
xor_def: THEORY
 BEGIN
  A, B: VAR bool
  XOR(A, B): bool = (A /= B)
  
  xor_def: LEMMA (A xor B) = IF A THEN NOT B ELSE B ENDIF
 END xor_def
```

#### quantifier_props

```
quantifier_props [t: TYPE]: THEORY
 BEGIN
  x: VAR t
  p, q: VAR [t -> bool]

  not_exists: LEMMA (EXISTS x: p(x)) = NOT (FORALL x: NOT p(x))

  exists_not: LEMMA (EXISTS x: NOT p(x)) = NOT (FORALL x: p(x))

  exists_or: LEMMA
    (EXISTS x: p(x) OR q(x)) = ((EXISTS x: p(x)) OR (EXISTS x: q(x)))

  exists_implies: LEMMA
    (EXISTS x: p(x) IMPLIES q(x)) = ((EXISTS x: NOT p(x)) OR (EXISTS x: q(x)))

  exists_and: LEMMA
    (EXISTS x: p(x) AND q(x)) IMPLIES ((EXISTS x: p(x)) AND (EXISTS x: q(x)))

  not_forall: LEMMA (FORALL x: p(x)) = NOT (EXISTS x: NOT p(x))

  forall_not: LEMMA (FORALL x: NOT p(x)) = NOT (EXISTS x: p(x))

  forall_and: LEMMA
    (FORALL x: p(x) AND q(x)) = ((FORALL x: p(x)) AND (FORALL x: q(x)))

  forall_or: LEMMA
    ((FORALL x: p(x)) OR (FORALL x: q(x))) IMPLIES (FORALL x: p(x) OR q(x))

 END quantifier_props  
```

#### defined_types  pred setof

```
defined_types [t: TYPE]: THEORY
 BEGIN
  pred: TYPE = [t -> bool]
  PRED: TYPE = [t -> bool]
  predicate: TYPE = [t -> bool]
  PREDICATE: TYPE = [t -> bool]
  setof: TYPE = [t -> bool]
  SETOF: TYPE = [t -> bool]
 END defined_types
```

#### exists1

```
exists1 [T: TYPE]: THEORY
 BEGIN
  x, y: VAR T
  p, q: VAR pred[T]        %%[t -> bool]

  unique?(p): bool = FORALL x, y: p(x) AND p(y) IMPLIES x = y

  exists1(p): bool = (EXISTS x: p(x)) AND unique?(p)

  unique_lem: LEMMA
    (FORALL x: p(x) IMPLIES q(x)) IMPLIES (unique?(q) IMPLIES unique?(p))

  exists1_lem: LEMMA (exists1! x: p(x)) IMPLIES (EXISTS x: p(x))

 END exists1
```

#### equality_props

```
equality_props[T: TYPE]: THEORY
 BEGIN

  x, y, z: VAR T
  b: VAR bool
  
  IF_true: POSTULATE IF true THEN x ELSE y ENDIF = x

  IF_false: POSTULATE IF false THEN x ELSE y ENDIF = y

  IF_same: LEMMA IF b THEN x ELSE x ENDIF = x

  reflexivity_of_equals: POSTULATE x = x

  transitivity_of_equals: POSTULATE x = y AND y = z IMPLIES x = z

  symmetry_of_equals: POSTULATE x = y IMPLIES y = x

 END equality_props
```

#### if_props

```
if_props [s, t: TYPE]: THEORY
 BEGIN
  a, b, c: VAR bool
  x, y: VAR s
  f: VAR [s -> t]
  
  lift_if1: LEMMA
    f(IF a THEN x ELSE y ENDIF) = IF a THEN f(x) ELSE f(y) ENDIF

  lift_if2: LEMMA
    IF (IF a THEN b ELSE c ENDIF) THEN x ELSE y ENDIF
     = IF a THEN (IF b THEN x ELSE y ENDIF)
            ELSE (IF c THEN x ELSE y ENDIF) ENDIF

 END if_props
```

#### functions

```
functions [D, R: TYPE]: THEORY
 BEGIN
  f, g: VAR [D -> R]
  x, x1, x2: VAR D
  y: VAR R
  Drel: VAR PRED[[D, D]]
  Rrel: VAR PRED[[R, R]]

  extensionality_postulate: POSTULATE
     (FORALL (x: D): f(x) = g(x)) IFF f = g

  % The extensionality lemma is provided as it is easier to use
  % as a rewrite than the above postulate.
  extensionality: LEMMA
     (FORALL (x: D): f(x) = g(x)) IMPLIES f = g

  congruence: POSTULATE f = g AND x1 = x2 IMPLIES f(x1) = g(x2)

  eta: LEMMA (LAMBDA (x: D): f(x)) = f

  injective?(f): bool = (FORALL x1, x2: (f(x1) = f(x2) => (x1 = x2)))

  surjective?(f): bool = (FORALL y: (EXISTS x: f(x) = y))

  bijective?(f): bool = injective?(f) & surjective?(f)

  bij_is_inj: JUDGEMENT (bijective?) SUBTYPE_OF (injective?)
  
  bij_is_surj: JUDGEMENT (bijective?) SUBTYPE_OF (surjective?)

  domain(f): TYPE = D

  range(f): TYPE = R

  graph(f)(x, y): bool = (f(x) = y)

  preserves(f, Drel, Rrel): bool =
      FORALL x1, x2: Drel(x1, x2) IMPLIES Rrel(f(x1), f(x2))

  % Curried form
  preserves(Drel, Rrel)(f): bool = preserves(f, Drel, Rrel)

  inverts(f, Drel, Rrel): bool =
      FORALL x1, x2: Drel(x1, x2) IMPLIES Rrel(f(x2), f(x1))

  % Curried form
  inverts(Drel, Rrel)(f): bool = inverts(f, Drel, Rrel)

 END functions
 
 
 functions_alt [D, R: TYPE, Drel: PRED[[D, D]], Rrel: PRED[[R, R]]]: THEORY
 BEGIN
  f: VAR [D -> R]
  preserves: [[D -> R] -> bool] = preserves(Drel, Rrel)
  inverts: [[D -> R] -> bool] = inverts(Drel, Rrel)
 END functions_alt


transpose[T1, T2, T3: TYPE]: THEORY
 BEGIN
  f: VAR [T1 -> [T2 -> T3]]
  x: VAR T1
  y: VAR T2

  transpose(f)(y)(x): T3 = f(x)(y)
 END transpose
```

#### restrict

```
restrict [T: TYPE, S: TYPE FROM T, R: TYPE]: THEORY
 BEGIN

  f: VAR [T -> R]
  s: VAR S

  restrict(f)(s): R = f(s)
  CONVERSION restrict

  injective_restrict: LEMMA
    injective?(f) IMPLIES injective?(restrict(f))

  restrict_of_inj_is_inj: JUDGEMENT
    restrict(f: (injective?[T,R])) HAS_TYPE (injective?[S,R])

 END restrict
 
 
 restrict_props[T: TYPE, R: TYPE]: THEORY
 BEGIN
  f: VAR [T -> R]
  restrict_full: LEMMA restrict[T, T, R](f) = f
 END restrict_props
```

#### relations

```
relations [T: TYPE]: THEORY
 BEGIN

  R: VAR PRED[[T, T]]
  x, y, z: VAR T

  eq: pred[[T, T]] = (LAMBDA x, y: x = y)

  reflexive?(R): bool = FORALL x: R(x, x)

  irreflexive?(R): bool = FORALL x: NOT R(x, x)

  symmetric?(R): bool = FORALL x, y: R(x, y) IMPLIES R(y, x)

  antisymmetric?(R): bool = FORALL x, y: R(x, y) & R(y, x) => x = y

  connected?(R): bool = FORALL x, y: x /= y IMPLIES R(x, y) OR R(y, x)

  transitive?(R): bool = FORALL x, y, z: R(x, y) & R(y, z) => R(x, z)

  equivalence?(R): bool = reflexive?(R) AND symmetric?(R) AND transitive?(R)

  equivalence: TYPE = (equivalence?)

  equiv_is_reflexive:  JUDGEMENT (equivalence?) SUBTYPE_OF (reflexive?)
  equiv_is_symmetric:  JUDGEMENT (equivalence?) SUBTYPE_OF (symmetric?)
  equiv_is_transitive: JUDGEMENT (equivalence?) SUBTYPE_OF (transitive?)

  % Reflexive closure
  RC(R)(x, y): bool = (x = y) OR R(x, y)

  % Transitive closure
  TC(R)(x, y): INDUCTIVE bool = R(x,y) OR (EXISTS z: TC(R)(x,z) AND TC(R)(z,y))

  % Reflexive-transitive closure
  RTC(R)(x, y): bool = (x = y) OR TC(R)(x, y)

 END relations
```

#### orders

```
orders [T: TYPE]: THEORY
 BEGIN
  x, y: VAR T
  <=, < : VAR pred[[T, T]]
  p: VAR pred[T]

  preorder?(<=): bool = reflexive?(<=) & transitive?(<=)

  preorder_is_reflexive:  JUDGEMENT (preorder?) SUBTYPE_OF (reflexive?[T])
  preorder_is_transitive: JUDGEMENT (preorder?) SUBTYPE_OF (transitive?[T])
  equiv_is_preorder:      JUDGEMENT (equivalence?[T]) SUBTYPE_OF (preorder?)

  partial_order?(<=): bool = preorder?(<=) & antisymmetric?(<=)

  po_is_preorder:      JUDGEMENT (partial_order?) SUBTYPE_OF (preorder?)
  po_is_antisymmetric: JUDGEMENT
    (partial_order?) SUBTYPE_OF (antisymmetric?[T])

  strict_order?(<): bool = irreflexive?(<) & transitive?(<)

  strict_is_irreflexive: JUDGEMENT
    (strict_order?) SUBTYPE_OF (irreflexive?[T])
  strict_order_is_antisymmetric: JUDGEMENT
    (strict_order?) SUBTYPE_OF (antisymmetric?[T])
  strict_is_transitive: JUDGEMENT
    (strict_order?) SUBTYPE_OF (transitive?[T])

  dichotomous?(<=): bool = (FORALL x, y: (x <= y OR y <= x))

  total_order?(<=): bool = partial_order?(<=) & dichotomous?(<=)

  total_is_po:          JUDGEMENT (total_order?) SUBTYPE_OF (partial_order?)
  total_is_dichotomous: JUDGEMENT (total_order?) SUBTYPE_OF (dichotomous?)

  linear_order?(<=): bool = total_order?(<=)

  linear_is_total: JUDGEMENT (linear_order?) SUBTYPE_OF (total_order?)
  total_is_linear: JUDGEMENT (total_order?)  SUBTYPE_OF (linear_order?)

  trichotomous?(<): bool = (FORALL x, y: x < y OR y < x OR x = y)

  strict_total_order?(<): bool = strict_order?(<) & trichotomous?(<)

  strict_total_is_strict: JUDGEMENT
    (strict_total_order?) SUBTYPE_OF (strict_order?)
  strict_total_is_trichotomous: JUDGEMENT
    (strict_total_order?) SUBTYPE_OF (trichotomous?)

  well_founded?(<): bool =
     (FORALL p:
        (EXISTS y: p(y))
	   IMPLIES (EXISTS (y:(p)): (FORALL (x:(p)): (NOT x < y))))

  strict_well_founded?(<): bool = strict_order?(<) & well_founded?(<)

  strict_well_founded_is_strict: JUDGEMENT
    (strict_well_founded?) SUBTYPE_OF (strict_order?)
  strict_well_founded_is_well_founded: JUDGEMENT
    (strict_well_founded?) SUBTYPE_OF (well_founded?)

  well_ordered?(<): bool = strict_total_order?(<) & well_founded?(<)

  well_ordered_is_strict_total: JUDGEMENT
    (well_ordered?) SUBTYPE_OF (strict_total_order?)
  well_ordered_is_well_founded: JUDGEMENT
    (well_ordered?) SUBTYPE_OF (well_founded?)

  nonempty_pred: TYPE = {p: pred[T] | EXISTS (x: T): p(x)}

  pe: VAR pred[T]

  upper_bound?(<)(x, pe): bool = FORALL (y: (pe)): y < x

  upper_bound?(<)(pe)(x): bool = upper_bound?(<)(x, pe)

  lower_bound?(<)(x, pe): bool = FORALL (y: (pe)): x < y

  lower_bound?(<)(pe)(x): bool = lower_bound?(<)(x, pe)

  least_upper_bound?(<)(x, pe): bool =
    upper_bound?(<)(x, pe) AND
      FORALL y: upper_bound?(<)(y, pe) IMPLIES (x < y OR x = y)

  least_upper_bound?(<)(pe)(x): bool = least_upper_bound?(<)(x, pe)

  greatest_lower_bound?(<)(x, pe): bool =
    lower_bound?(<)(x, pe) AND
      FORALL y: lower_bound?(<)(y, pe) IMPLIES (y < x OR x = y)

  greatest_lower_bound?(<)(pe)(x): bool = greatest_lower_bound?(<)(x, pe)

 END orders
```

#### epsilons

```
epsilons [T: NONEMPTY_TYPE]: THEORY
 BEGIN
  p: VAR pred[T]
  x: VAR T

  epsilon(p): T

  epsilon_ax: AXIOM (EXISTS x: p(x)) => p(epsilon(p))

 END epsilons
```

#### sets

```
sets [T: TYPE]: THEORY
 BEGIN

  set: TYPE = setof[T]

  x, y: VAR T

  a, b, c: VAR set

  p: VAR PRED[T]

  member(x, a): bool = a(x)

  empty?(a): bool = (FORALL x: NOT member(x, a))

  emptyset: set = {x | false}

  nonempty?(a): bool = NOT empty?(a)

  nonempty_set: TYPE = (nonempty?)

  full?(a): bool = (FORALL x: member(x, a))

  fullset: set = {x | true}

  nontrivial?(a): bool = a /= emptyset & a /= fullset

  subset?(a, b): bool = (FORALL x: member(x, a) => member(x, b))

  strict_subset?(a, b): bool = subset?(a, b) & a /= b

  union(a, b): set = {x | member(x, a) OR member(x, b)}

  intersection(a, b): set = {x | member(x, a) AND member(x, b)}

  disjoint?(a, b): bool = empty?(intersection(a, b))

  complement(a): set = {x | NOT member(x, a)} 

  difference(a, b): set = {x | member(x, a) AND NOT member(x, b)}

  symmetric_difference(a, b): set =
    union(difference(a, b), difference(b, a))

  every(p)(a): bool = FORALL (x:(a)): p(x)

  every(p, a): bool = FORALL (x:(a)): p(x)

  some(p)(a): bool = EXISTS (x:(a)): p(x)

  some(p, a): bool = EXISTS (x:(a)): p(x)

  singleton?(a): bool = (EXISTS (x:(a)): (FORALL (y:(a)): x = y))

  singleton(x): (singleton?) = {y | y = x}

  % CONVERSION+ singleton

  add(x, a): (nonempty?) = {y | x = y OR member(y, a)}

  remove(x, a): set = {y | x /= y AND member(y, a)}

  % A choice function for nonempty sets
  % No longer defined, so that grind will not expand epsilon.
  % It's generally easier to use typepred on choose than to bring in epsilon_ax
  % Can always bring in choose_is_epsilon axiom if the expansion is desired.
  choose(p: (nonempty?)): (p) % = epsilon(p)

  choose_is_epsilon: AXIOM FORALL (p: (nonempty?)): choose(p) = epsilon(p)

  the(p: (singleton?)): (p)

  the_lem: LEMMA FORALL (p: (singleton?)): the(p) = epsilon(p)

  the_prop: LEMMA FORALL (p: (singleton?)): p(the(p))

  singleton_elt(a: (singleton?)): T = the! x: member(x, a)

  CONVERSION+ singleton_elt

  is_singleton: LEMMA
    FORALL a: (nonempty?(a) AND FORALL x, y: a(x) AND a(y) IMPLIES (x=y))
      IMPLIES singleton?(a)

  singleton_elt_lem: LEMMA singleton?(a) and a(x) IMPLIES singleton_elt(a) = x

  singleton_elt_def: LEMMA singleton?(a) IMPLIES singleton_elt(a) = choose(a)

  singleton_singleton: LEMMA singleton?(a) IMPLIES (EXISTS x: a = singleton(x))

  singleton_rew: LEMMA singleton_elt(singleton(x)) = x

  AUTO_REWRITE+ singleton_rew

  rest(a): set = IF empty?(a) THEN a ELSE remove(choose(a), a) ENDIF

  setofsets: TYPE = setof[setof[T]]
  
  A, B : Var setofsets

  powerset(a): setofsets = {b | subset?(b, a)}

  Union(A): set = {x | EXISTS (a: (A)): a(x)}

  Intersection(A): set = {x | FORALL (a: (A)): a(x)}

  nonempty_singleton: JUDGEMENT (singleton?) SUBTYPE_OF (nonempty?)
  nonempty_union1: JUDGEMENT union(a: (nonempty?), b: set) HAS_TYPE (nonempty?)
  nonempty_union2: JUDGEMENT union(a: set, b: (nonempty?)) HAS_TYPE (nonempty?)

 END sets
```

####sets_lemmas

```

sets_lemmas [T: TYPE]: THEORY
 BEGIN

  x, y: VAR T
  a, b, c: VAR set[T]
  A, B : Var setofsets[T]

  extensionality: LEMMA
      (FORALL x: member(x, a) IFF member(x, b)) IMPLIES (a = b)

  emptyset_is_empty?: LEMMA  empty?(a) IFF a = emptyset

  empty_no_members: LEMMA NOT member(x, emptyset)

  emptyset_min: LEMMA subset?(a, emptyset) IMPLIES a = emptyset

  nonempty_member: LEMMA nonempty?(a) IFF EXISTS x: member(x, a)

  fullset_member: LEMMA member(x, fullset)

  fullset_max: LEMMA subset?(fullset, a)  IMPLIES a = fullset

  fullset_is_full?: LEMMA full?(a) IFF a = fullset
  
  nonempty_exists: LEMMA nonempty?(a) IFF EXISTS (x: (a)): TRUE

  subset_emptyset: LEMMA subset?(emptyset, a)

  subset_fullset: LEMMA subset?(a, fullset)

  subset_reflexive: LEMMA subset?(a, a)

  subset_antisymmetric: LEMMA subset?(a, b) AND subset?(b, a) IMPLIES a = b

  subset_transitive: LEMMA
    subset?(a, b) AND subset?(b, c) IMPLIES subset?(a, c)

  subset_partial_order: LEMMA partial_order?(subset?[T])

  subset_is_partial_order: JUDGEMENT
    subset?[T] HAS_TYPE (partial_order?[set[T]])

  strict_subset_irreflexive: LEMMA NOT strict_subset?(a, a)

  strict_subset_transitive: LEMMA
    strict_subset?(a, b) AND strict_subset?(b, c) IMPLIES
     strict_subset?(a, c)

  strict_subset_strict_order: LEMMA strict_order?(strict_subset?[T])

  strict_subset_is_strict_order: JUDGEMENT
    strict_subset?[T] HAS_TYPE (strict_order?[set[T]])

  union_idempotent: LEMMA union(a, a) = a

  union_commutative: LEMMA union(a, b) = union(b, a)

  union_associative: LEMMA union(union(a, b), c) = union(a, union(b, c))

  union_empty: LEMMA union(a, emptyset) = a

  union_full: LEMMA union(a, fullset) = fullset

  union_subset1: LEMMA subset?(a, union(a, b))

  union_subset2: LEMMA subset?(a, b) IMPLIES union(a, b) = b

  subset_union: LEMMA subset?(union(a, b), c) = (subset?(a, c) AND subset?(b, c)) 

  union_upper_bound : LEMMA
    subset?(a, c) and subset?(b, c) IMPLIES subset?(union(a, b), c)
  
  union_difference: LEMMA union(a, b) = union(a, difference(b, a))

  union_diff_subset: LEMMA subset?(a, b) IMPLIES union(a, difference(b, a)) = b

  intersection_idempotent: LEMMA intersection(a, a) = a

  intersection_commutative: LEMMA intersection(a, b) = intersection(b, a)

  intersection_associative: LEMMA
    intersection(intersection(a, b), c) = intersection(a, intersection(b, c))

  intersection_empty: LEMMA intersection(a, emptyset) = emptyset

  intersection_full: LEMMA intersection(a, fullset) = a

  intersection_subset1: LEMMA subset?(intersection(a, b), a)

  intersection_subset2: LEMMA subset?(a, b) IMPLIES intersection(a, b) = a

  intersection_lower_bound: LEMMA
    subset?(c, a) and subset?(c, b) IMPLIES subset?(c, intersection(a, b))

  distribute_intersection_union: LEMMA
    intersection(a, union(b, c))
        = union(intersection(a, b), intersection(a, c))

  distribute_union_intersection: LEMMA
    union(a, intersection(b, c)) = intersection(union(a, b), union(a, c))

  complement_emptyset: LEMMA complement(emptyset[T]) = fullset

  complement_fullset: LEMMA complement(fullset[T]) = emptyset

  complement_complement: LEMMA complement(complement(a)) = a

  complement_equal: LEMMA complement(a) = complement(b) IFF a = b

  subset_complement: LEMMA
    subset?(complement(a), complement(b)) IFF subset?(b, a)

  demorgan1: LEMMA
    complement(union(a, b)) = intersection(complement(a), complement(b))

  demorgan2: LEMMA
    complement(intersection(a, b)) = union(complement(a), complement(b))

  difference_emptyset1: LEMMA difference(a, emptyset) = a

  difference_emptyset2: LEMMA difference(emptyset, a) = emptyset

  difference_fullset1: LEMMA difference(a, fullset) = emptyset

  difference_fullset2: LEMMA difference(fullset, a) = complement(a)

  difference_intersection: LEMMA
    difference(a, b) = intersection(a, complement(b))

  difference_difference1: LEMMA
    difference(difference(a, b), c) = difference(a, union(b, c))

  difference_difference2: LEMMA
    difference(a, difference(b, c))
      = union(difference(a, b), intersection(a, c))

  difference_subset : LEMMA  subset?(difference(a, b), a)

  difference_subset2: LEMMA
    subset?(a, b) IMPLIES difference(a, b) = emptyset

  difference_disjoint: LEMMA disjoint?(a, difference(b, a))

  difference_disjoint2: LEMMA disjoint?(a, b) IMPLIES difference(a, b) = a

  diff_union_inter: LEMMA
    difference(union(a, b), a) = difference(b, intersection(a, b))

  nonempty_add: LEMMA NOT empty?(add(x, a))

  member_add: LEMMA member(x, a) IMPLIES add(x, a) = a

  member_add_reduce: LEMMA member(x, add(y, a)) = (x = y or member(x, a))

  member_remove: LEMMA NOT member(x, a) IMPLIES remove(x, a) = a

  add_remove_member: LEMMA member(x, a) IMPLIES add(x, remove(x, a)) = a

  remove_add_member: LEMMA NOT member(x, a) IMPLIES remove(x, add(x, a)) = a

  subset_add: LEMMA subset?(a, add(x, a))

  add_as_union: LEMMA add(x, a) = union(a, singleton(x))

  singleton_as_add: LEMMA singleton(x) = add(x, emptyset)

  subset_remove: LEMMA subset?(remove(x, a), a)

  remove_as_difference: LEMMA remove(x, a) = difference(a, singleton(x))

  remove_member_singleton: LEMMA remove(x, singleton(x)) = emptyset

  choose_rest: LEMMA NOT empty?(a) IMPLIES add(choose(a), rest(a)) = a

  choose_member: LEMMA NOT empty?(a) IMPLIES member(choose(a), a)
  
  choose_not_member: LEMMA
      NOT empty?(a) IMPLIES NOT member(choose(a), rest(a))
  
  rest_not_equal: LEMMA NOT empty?(a) IMPLIES rest(a) /= a

  rest_member: LEMMA member(x,rest(a)) IMPLIES member(x,a)

  rest_subset : LEMMA subset?(rest(a), a)

  choose_add: LEMMA choose(add(x, a)) = x OR member(choose(add(x, a)), a)

  choose_rest_or: LEMMA
    member(x,a) IMPLIES member(x,rest(a)) OR x = choose(a)

  choose_singleton: LEMMA choose(singleton(x)) = x

  rest_singleton: LEMMA rest(singleton(x)) = emptyset[T]
  
  singleton_subset: LEMMA member(x, a) IFF subset?(singleton(x), a)

  rest_empty_lem: LEMMA NOT empty?(a) AND empty?(rest(a))
                          IMPLIES a = singleton(choose(a))

  singleton_disjoint: LEMMA NOT member(x, a) IMPLIES disjoint?(singleton(x), a)

  disjoint_remove_left: LEMMA
    disjoint?(a, b) IMPLIES disjoint?(remove(x, a), b)

  disjoint_remove_right: LEMMA
    disjoint?(a, b) IMPLIES disjoint?(a, remove(x, b))

  union_disj_remove_left: LEMMA
    disjoint?(a, b) AND a(x) IMPLIES
      union(remove(x, a), b) = remove(x, union(a, b))

  union_disj_remove_right: LEMMA
    disjoint?(a, b) AND b(x) IMPLIES
      union(a, remove(x, b)) = remove(x, union(a, b))

  subset_powerset: LEMMA subset?(a, b) IFF member(a, powerset(b))

  empty_powerset: LEMMA empty?(a) IFF singleton?(powerset(a))

  powerset_emptyset: LEMMA member(emptyset, powerset(a))

  nonempty_powerset: JUDGEMENT powerset(a) HAS_TYPE (nonempty?[set[T]])

  powerset_union: LEMMA Union(powerset(a)) = a

  powerset_intersection: LEMMA empty?(Intersection(powerset(a)))

  powerset_subset: LEMMA subset?(a, b) IFF subset?(powerset(a), powerset(b))

  Union_empty: LEMMA empty?(Union(A)) IFF empty?(A) OR every(empty?)(A)

  Union_full: LEMMA
    full?(Union(A)) IFF (FORALL x: EXISTS (a: (A)): member(x, a))

  Union_member: LEMMA member(x,Union(A)) IFF (EXISTS (a:(A)): member(x,a))

  Union_subset: LEMMA FORALL (a: (A)): subset?(a, Union(A))

  Union_surjective: JUDGEMENT
    Union HAS_TYPE (surjective?[setofsets[T], set[T]])

  Union_emptyset_rew: LEMMA Union(emptyset[set[T]]) = emptyset

  Union_union_rew: LEMMA nonempty?(A) IMPLIES
                            Union(A) = union(choose(A), Union(rest(A)))

  Intersection_empty: LEMMA
    empty?(Intersection(A)) IFF
     (FORALL x: EXISTS (a: (A)): NOT member(x, a))

  Intersection_full: LEMMA full?(Intersection(A)) IFF every(full?)(A)

  Intersection_member: LEMMA
    member(x,Intersection(A)) IFF (FORALL (a:(A)): member(x,a))

END sets_lemmas
```

#### relation_defs

```
relation_defs [T1, T2: TYPE]: THEORY
 BEGIN
  R, S: VAR pred[[T1, T2]]
  x: VAR T1
  y: VAR T2
  X: VAR set[T1]
  Y: VAR set[T2]

  domain?(R)(x: T1): bool = EXISTS (y: T2): R(x, y)

  range?(R)(y: T2): bool = EXISTS (x: T1): R(x, y)

  domain(R): TYPE = {x: T1 | EXISTS (y: T2): R(x, y)}

  range(R): TYPE = {y: T2 | EXISTS (x: T1): R(x, y)}

  rinverse(R)(y, x): bool = R(x, y)

  rcomplement(R)(x, y): bool = NOT R(x, y)

  runion(R, S)(x, y): bool = R(x, y) OR S(x, y)

  rintersection(R, S)(x, y): bool = R(x, y) AND S(x, y)

  image(R, X): set[T2] = {y: T2 | EXISTS (x: (X)): R(x, y)}

  image(R)(X): set[T2] = image(R, X)

  preimage(R, Y): set[T1] = {x: T1 | EXISTS (y: (Y)): R(x, y)}

  preimage(R)(Y): set[T1] = preimage(R, Y)

  postcondition(R, X): set[T2] = {y: T2 | EXISTS (x: (X)): R(x, y)}

  postcondition(R)(X): set[T2] = postcondition(R, X)

  precondition(R, Y): set[T1] = {x: T1 | FORALL (y: T2 | R(x, y)): Y(y)}

  precondition(R)(Y): set[T1] = precondition(R, Y)

  converse(R): pred[[T2, T1]] = (LAMBDA (y: T2), (x: T1): R(x, y))

  isomorphism?(R): bool =
    (EXISTS (f: (bijective?[T1, T2])): R = graph(f))

  total?(R): bool = FORALL (x: T1): EXISTS (y: T2): R(x, y)

  onto?(R): bool = FORALL (y: T2): EXISTS (x: T1): R(x, y)

 END relation_defs
```

#### relation_props

```
relation_props [T1, T2, T3: TYPE]: THEORY
 BEGIN
  R1: VAR pred[[T1, T2]]
  R2: VAR pred[[T2, T3]]
  x: VAR T1
  y: VAR T2
  z: VAR T3

  o(R1, R2)(x, z): bool = EXISTS y: R1(x, y) AND R2(y, z)

  total_composition: LEMMA total?(R1) & total?(R2) => total?(R1 o R2)

  onto_composition: LEMMA onto?(R1) & onto?(R2) => onto?(R1 o R2)

  composition_total: JUDGEMENT
    o(R1: (total?[T1, T2]), R2: (total?[T2, T3])) HAS_TYPE (total?[T1, T3])

  composition_onto: JUDGEMENT
    o(R1: (onto?[T1, T2]), R2: (onto?[T2, T3])) HAS_TYPE (onto?[T1, T3])

 END relation_props
 
 
 relation_props2 [T1, T2, T3, T4: TYPE]: THEORY
 BEGIN
  R1: VAR pred[[T1, T2]]
  R2: VAR pred[[T2, T3]]
  R3: VAR pred[[T3, T4]]

  assoc: LEMMA (R1 o R2) o R3 = R1 o (R2 o R3)
 END relation_props2

```

#### indexed_sets

```
indexed_sets[index, T: TYPE]: THEORY
 BEGIN
  i: VAR index
  x: VAR T
  A, B: VAR [index -> set[T]]
  S: VAR set[T]

  IUnion(A): set[T] = {x | EXISTS i: A(i)(x)}

  IIntersection(A): set[T] = {x | FORALL i: A(i)(x)}

  IUnion_Union: LEMMA
    IUnion(A) = Union(image(A)(fullset[index]))

  IIntersection_Intersection: LEMMA
    IIntersection(A) = Intersection(image(A)(fullset[index]))

  IUnion_union: LEMMA
    IUnion(LAMBDA i: union(A(i), B(i))) = union(IUnion(A), IUnion(B))

  IIntersection_intersection: LEMMA
    IIntersection(LAMBDA i: intersection(A(i), B(i)))
        = intersection(IIntersection(A), IIntersection(B))

  IUnion_intersection: LEMMA
    IUnion(LAMBDA i: intersection(A(i), S))
        = intersection(IUnion(A), S)

  IIntersection_union: LEMMA
    IIntersection(LAMBDA i: union(A(i), S))
        = union(IIntersection(A), S)
 END indexed_sets
```

#### operator_defs

```
operator_defs[T: TYPE]: THEORY
 BEGIN
  o, *: VAR [T, T -> T]
  -: VAR [T -> T]
  x, y, z: VAR T

  commutative?(o):     bool = (FORALL x, y: x o y = y o x)

  associative?(o):     bool = (FORALL x, y, z: (x o y) o z = x o (y o z))

  left_identity?(o)(y): bool = (FORALL x: y o x = x)

  right_identity?(o)(y): bool = (FORALL x: x o y = x)

  identity?(o)(y):     bool = (FORALL x: x o y = x AND y o x = x)

  has_identity?(o):    bool = (EXISTS y: identity?(o)(y))

  zero?(o)(y):         bool = (FORALL x: x o y = y AND y o x = y)

  has_zero?(o):        bool = (EXISTS y: zero?(o)(y))

  inverses?(o)(-)(y):  bool = (FORALL x: x o -x = y AND (-x) o x = y)

  has_inverses?(o):    bool = (EXISTS -, y: inverses?(o)(-)(y))

  distributive?(*, o): bool = (FORALL x, y, z: x * (y o z) = (x * y) o (x * z))

 END operator_defs
```

#### numbers

```
numbers: THEORY
 BEGIN

  number: NONEMPTY_TYPE

 END numbers
```

```
number_fields: THEORY
 BEGIN
  number_field: NONEMPTY_TYPE FROM number
  numfield: NONEMPTY_TYPE = number_field
  number_field?(n: number): bool = number_field_pred(n)

  % The following declarations, if they could be expanded, are built in to
  %  the typechecker and prover:
  % 0, 1, 2, ... : number_field
  % AXIOM 0 /= 1 AND 0 /= 2 AND 1 /= 2 AND ...

  nonzero_number: NONEMPTY_TYPE = {r: number_field | r /= 0} CONTAINING 1
  nznum: NONEMPTY_TYPE = nonzero_number

  +, -, *: [numfield, numfield -> numfield]
  /: [numfield, nznum -> numfield]
  -: [numfield -> numfield]

  % Field Axioms - these are not provable using the decision
  % procedures, as the operators are translated to uninterpreted
  % functions, so that x * x = -1 is consistent.  When the
  % arguments are (a subtype of) real, then the translation is to
  % interpreted symbols.

  x, y, z: VAR numfield
  n0x: VAR nznum

  commutative_add : POSTULATE x + y = y + x

  associative_add : POSTULATE x + (y + z) = (x + y) + z

  identity_add    : POSTULATE x + 0 = x

  inverse_add     : AXIOM x + -x = 0

  minus_add       : AXIOM x - y = x + -y

  commutative_mult: AXIOM x * y = y * x

  associative_mult: AXIOM x * (y * z) =  (x * y) * z

  identity_mult   : AXIOM 1 * x = x

  inverse_mult    : AXIOM n0x * (1/n0x) = 1

  div_def         : AXIOM y/n0x = y * (1/n0x)

  distributive    : POSTULATE x * (y + z) = (x * y) + (x * z)

 END number_fields
```

```
reals: THEORY
 BEGIN

  real: NONEMPTY_TYPE FROM number_field

  real?(n:number): bool = number_field_pred(n) AND real_pred(n)

  % The following declarations, if they could be expanded, are built in to
  %  the typechecker:
  % AXIOM real_pred(0) AND real_pred(1) AND real_pred(2) AND ...
  % JUDGEMENT 0, 1, 2, ... HAS_TYPE real

  nonzero_real: NONEMPTY_TYPE = {r: real | r /= 0} CONTAINING 1
  nzreal: NONEMPTY_TYPE = nonzero_real
  % nzreal_is_nznum: JUDGEMENT nonzero_real SUBTYPE_OF nonzero_number

  x, y: VAR real
  n0z: VAR nzreal

  closed_plus:    AXIOM real_pred(x + y)
  closed_minus:   AXIOM real_pred(x - y)
  closed_times:   AXIOM real_pred(x * y)
  closed_divides: AXIOM real_pred(x / n0z)
  closed_neg:     AXIOM real_pred(-x)

  real_plus_real_is_real:  JUDGEMENT +(x,y) HAS_TYPE real
  real_minus_real_is_real: JUDGEMENT -(x,y) HAS_TYPE real
  real_times_real_is_real: JUDGEMENT *(x,y) HAS_TYPE real
  real_div_nzreal_is_real: JUDGEMENT /(x,n0z) HAS_TYPE real

  minus_real_is_real: JUDGEMENT -(x) HAS_TYPE real

  <(x, y):  bool
  <=(x, y): bool = x < y OR x = y;
  >(x, y):  bool = y < x;
  >=(x, y): bool = y <= x

  reals_totally_ordered: POSTULATE strict_total_order?(<)

  % Built in:
  % AXIOM 0 < 1 AND 1 < 2 AND ...

 END reals
```

```
real_axioms: THEORY
 BEGIN
  x, y, z: VAR real
  n0x: VAR nzreal

  % Field Axioms are now in number_fields

  % Order Axioms

  posreal_add_closed : POSTULATE x > 0 AND y > 0 IMPLIES x + y > 0

  posreal_mult_closed: AXIOM x > 0 AND y > 0 IMPLIES x * y > 0

  posreal_neg        : POSTULATE x > 0 IMPLIES NOT -x > 0

  trichotomy         : POSTULATE x > 0 OR x = 0 OR 0 > x


  % Completeness Axiom defined in bounded_reals

 END real_axioms
```

```
bounded_real_defs: THEORY
BEGIN

  x, y: VAR real

  % Completeness Axiom

  S: VAR (nonempty?[real])

  upper_bound?(x, S): bool = FORALL (s: (S)): s <= x

  upper_bound?(S)(x): bool = upper_bound?(x, S)

  lower_bound?(x, S): bool = FORALL (s: (S)): x <= s

  lower_bound?(S)(x): bool = lower_bound?(x, S)

  least_upper_bound?(x, S): bool =
    upper_bound?(x, S) AND
      FORALL y: upper_bound?(y, S) IMPLIES (x <= y)

  least_upper_bound?(S)(x): bool = least_upper_bound?(x, S)

  greatest_lower_bound?(x, S): bool =
    lower_bound?(x, S) AND
      FORALL y: lower_bound?(y, S) IMPLIES (y <= x)

  greatest_lower_bound?(S)(x): bool = greatest_lower_bound?(x, S)

  real_complete: AXIOM
    FORALL S:
      (EXISTS y: upper_bound?(y, S)) IMPLIES
        (EXISTS y: least_upper_bound?(y, S))

  real_lower_complete: LEMMA
    FORALL S:
      (EXISTS y: lower_bound?(y, S)) IMPLIES 
        (EXISTS x: greatest_lower_bound?(x, S))


  % lub and glb

  bounded_above?(S): bool = (EXISTS x: upper_bound?(x, S))

  bounded_below?(S): bool = (EXISTS x: lower_bound?(x, S))

  bounded?(S): bool = bounded_above?(S) AND bounded_below?(S)

  bounded_set: TYPE = (bounded?)

  SA: VAR (bounded_above?)

  lub_exists: LEMMA (EXISTS x: least_upper_bound?(x, SA))

  lub(SA): {x | least_upper_bound?(x, SA)}

  lub_lem: LEMMA lub(SA) = x IFF least_upper_bound?(x, SA)

  SB: VAR (bounded_below?)

  glb_exists: LEMMA (EXISTS x: greatest_lower_bound?(x, SB))

  glb(SB): {x | greatest_lower_bound?(x, SB)}

  glb_lem: LEMMA glb(SB) = x IFF greatest_lower_bound?(x, SB)

END bounded_real_defs
```

```
min_nat[T: TYPE FROM nat]: THEORY
 BEGIN
  S: VAR (nonempty?[T])
  a, x: VAR T

  min(S): {a | S(a) AND (FORALL x: S(x) IMPLIES a <= x)}

  minimum?(a, S) : bool = S(a) AND (FORALL x: S(x) IMPLIES a <= x)

  min_def : LEMMA 
     FORALL (S: (nonempty?[T])) : min(S) = a IFF minimum?(a, S)

 END min_nat
```



####real_defs

```
real_defs: THEORY
 BEGIN
  m, n: VAR real

  sgn(m): int = IF m >= 0 THEN 1 ELSE -1 ENDIF

  abs(m): {n: nonneg_real | n >= m AND n >= -m}
    = IF m < 0 THEN -m ELSE m ENDIF

  nonzero_abs_is_pos: JUDGEMENT abs(x:nzreal) HAS_TYPE {y: posreal | y >= x}
  rat_abs_is_nonneg: JUDGEMENT abs(q:rat) HAS_TYPE {r: nonneg_rat | r >= q}
  nzrat_abs_is_pos: JUDGEMENT abs(q:nzrat) HAS_TYPE {r: posrat | r >= q}
  int_abs_is_nonneg: JUDGEMENT abs(i:int) HAS_TYPE {j: nonneg_int | j >= i}
  nzint_abs_is_pos: JUDGEMENT abs(i:nzint) HAS_TYPE {j: posint | j >= i}

  max(m, n): {p: real | p >= m AND p >= n}
    = IF m < n THEN n ELSE m ENDIF

  min(m, n): {p: real | p <= m AND p <= n}
    = IF m > n THEN n ELSE m ENDIF

  % real subtype judgements

  nzreal_max: JUDGEMENT
    max(x,y:nzreal) HAS_TYPE {z: nzreal | z >= x AND z >= y}
  nzreal_min: JUDGEMENT
    min(x,y:nzreal) HAS_TYPE {z: nzreal | z <= x AND z <= y}

  nonneg_real_max: JUDGEMENT
    max(x,y:nonneg_real) HAS_TYPE {z: nonneg_real | z >= x AND z >= y}
  nonneg_real_min: JUDGEMENT
    min(x,y:nonneg_real) HAS_TYPE {z: nonneg_real | z <= x AND z <= y}

  posreal_max: JUDGEMENT
    max(x,y:posreal) HAS_TYPE {z: posreal | z >= x AND z >= y}
  posreal_min: JUDGEMENT
    min(x,y:posreal) HAS_TYPE {z: posreal | z <= x AND z <= y}

  % rat subtype judgements

  rat_max: JUDGEMENT
    max(q,r:rat) HAS_TYPE {s: rat | s >= q AND s >= r}
  rat_min: JUDGEMENT
    min(q,r:rat) HAS_TYPE {s: rat | s <= q AND s <= r}

  nzrat_max: JUDGEMENT
    max(q,r:nzrat) HAS_TYPE {s: nzrat | s >= q AND s >= r}
  nzrat_min: JUDGEMENT
    min(q,r:nzrat) HAS_TYPE {s: nzrat | s <= q AND s <= r}

  nonneg_rat_max: JUDGEMENT
    max(q,r:nonneg_rat) HAS_TYPE {s: nonneg_rat | s >= q AND s >= r}
  nonneg_rat_min: JUDGEMENT
    min(q,r:nonneg_rat) HAS_TYPE {s: nonneg_rat | s <= q AND s <= r}

  posrat_max: JUDGEMENT
    max(q,r:posrat) HAS_TYPE {s: posrat | s >= q AND s >= r}
  posrat_min: JUDGEMENT
    min(q,r:posrat) HAS_TYPE {s: posrat | s <= q AND s <= r}

  % int subtype judgements

  int_max: JUDGEMENT
    max(i,j:int) HAS_TYPE {k: int | i <= k AND j <= k}
  int_min: JUDGEMENT
    min(i,j:int) HAS_TYPE {k: int | k <= i AND k <= j}

  nzint_max: JUDGEMENT
    max(i,j:nzint) HAS_TYPE {k: nzint | i <= k AND j <= k}
  nzint_min: JUDGEMENT
    min(i,j:nzint) HAS_TYPE {k: nzint | k <= i AND k <= j}

  nat_max: JUDGEMENT
    max(i,j:nat) HAS_TYPE {k: nat | i <= k AND j <= k}
  nat_min: JUDGEMENT
    min(i,j:nat) HAS_TYPE {k: nat | k <= i AND k <= j}

  posint_max: JUDGEMENT
    max(i,j:posint) HAS_TYPE {k: posint | i <= k AND j <= k}
  posint_min: JUDGEMENT
    min(i,j:posint) HAS_TYPE {k: posint | k <= i AND k <= j}

  % Useful facts about maximum and minimum of two reals
  %
  % Author : Alfons Geser, National Institute of Aerospace
  % Date   : Aug 26, 2003
  a,b,c : VAR real

  min_le : LEMMA min(a,b) <= c IFF (a <= c OR b <= c)
  min_lt : LEMMA min(a,b) < c IFF (a < c OR b < c)
  min_ge : LEMMA min(a,b) >= c IFF (a >= c AND b >= c)
  min_gt : LEMMA min(a,b) > c IFF (a > c AND b > c)
  le_min : LEMMA a <= min(b,c) IFF (a <= b AND a <= c)
  lt_min : LEMMA a < min(b,c) IFF (a < b AND a < c)
  ge_min : LEMMA a >= min(b,c) IFF (a >= b OR a >= c)
  gt_min : LEMMA a > min(b,c) IFF (a > b OR a > c)

  max_le : LEMMA max(a,b) <= c IFF (a <= c AND b <= c)
  max_lt : LEMMA max(a,b) < c IFF (a < c AND b < c)
  max_ge : LEMMA max(a,b) >= c IFF (a >= c OR b >= c)
  max_gt : LEMMA max(a,b) > c IFF (a > c OR b > c)
  le_max : LEMMA a <= max(b,c) IFF (a <= b OR a <= c)
  lt_max : LEMMA a < max(b,c) IFF (a < b OR a < c)
  ge_max : LEMMA a >= max(b,c) IFF (a >= b AND a >= c)
  gt_max : LEMMA a > max(b,c) IFF (a > b AND a > c)

 END real_defs
```

#### floor_ceil

```
floor_ceil: THEORY
 BEGIN
  
  x, y: VAR real
  py:  VAR posreal
  j: VAR nonzero_integer
  i, k: VAR integer
  
  floor_exists: LEMMA EXISTS i: i <= x & x < i + 1
  
  ceiling_exists: LEMMA EXISTS i: x <= i & i < x + 1
  
  floor(x): {i | i <= x & x < i + 1}
  
  fractional(x): {x | 0 <= x & x < 1} = x - floor(x)
  
  ceiling(x): {i | x <= i & i < x + 1}
  
  floor_def: LEMMA floor(x) <= x & x < floor(x) + 1
  
  ceiling_def: LEMMA x <= ceiling(x) & ceiling(x) < x + 1
  
  floor_ceiling_reflect1: LEMMA floor(-x) = -ceiling(x)
  
  floor_ceiling_reflect2: LEMMA ceiling(-x) = -floor(x)
  
  nonneg_floor_is_nat: JUDGEMENT floor(x:nonneg_real) HAS_TYPE nat
  nonneg_ceiling_is_nat: JUDGEMENT ceiling(x:nonneg_real) HAS_TYPE nat
  
  floor_int: LEMMA floor(i) = i
  
  ceiling_int: LEMMA ceiling(i) = i
  
  floor_plus_int: LEMMA floor(x + i) = floor(x) + i
  
  ceiling_plus_int: LEMMA ceiling(x + i) = ceiling(x) + i
  
  floor_ceiling_nonint: LEMMA NOT integer?(x) => ceiling(x) - floor(x) = 1

  floor_ceiling_int: lemma floor(i)=ceiling(i)
  
  floor_neg: LEMMA 
        floor(x) = IF integer?(x) THEN -floor(-x) ELSE -floor(-x) - 1 ENDIF
  
  real_parts: LEMMA x = floor(x) + fractional(x)
  
  floor_plus: LEMMA 
        floor(x + y) = floor(x) + floor(y) + floor(fractional(x) + fractional(y))
  
  ceiling_plus: LEMMA
        ceiling(x + y) = floor(x) + floor(y) + ceiling(fractional(x) + fractional(y))
  
  floor_split: lemma i = floor(i/2)+ceiling(i/2)

  floor_within_1: lemma x - floor(x) < 1

  ceiling_within_1: lemma ceiling(x) - x < 1

  floor_val:   LEMMA i >= k*j AND i < (k+1)*j IMPLIES floor(i/j) = k 

  floor_small: LEMMA  abs(i) < abs(j) IMPLIES
                         floor(i/j) = IF i/j >= 0 THEN 0 ELSE -1 ENDIF

  floor_eq_0:  LEMMA floor(x) = 0 IMPLIES x >= 0 AND x < 1

  fractional_plus: LEMMA
        fractional(x+y) = fractional(fractional(x) + fractional(y))

  floor_div: LEMMA floor(x/py) = i IFF py*i <= x AND x < py*(i+1)
  floor_0:   LEMMA floor(x)    = 0 IFF 0    <= x AND x < 1

 END floor_ceil
```

####exponentiation幂

```
exponentiation: THEORY
 BEGIN
  r: VAR real
  q: VAR rat
  nnq: VAR nonneg_rat
  m, n: VAR nat
  pm, pn: VAR posnat
  i, j: VAR int
  n0i, n0j: VAR nzint
  x, y: VAR real
  px, py: VAR posreal
  n0x, n0y: VAR nzreal
  gt1x, gt1y: VAR {r: posreal | r > 1}
  lt1x, lt1y: VAR {r: posreal | r < 1}
  ge1x, ge1y: VAR {r: posreal | r >= 1}
  le1x, le1y: VAR {r: posreal | r <= 1}

  expt(r, n): RECURSIVE real =
    IF n = 0 THEN 1
    ELSE r * expt(r, n-1)
    ENDIF
   MEASURE n;

  expt_pos_aux: LEMMA expt(px, n) > 0

  expt_nonzero_aux: LEMMA expt(n0x, n) /= 0;

  nnreal_expt:  JUDGEMENT expt(x:nnreal,  n:nat) HAS_TYPE nnreal
  posreal_expt: JUDGEMENT expt(x:posreal, n:nat) HAS_TYPE posreal
  nzreal_expt:  JUDGEMENT expt(x:nzreal,  n:nat) HAS_TYPE nzreal
  rat_expt:     JUDGEMENT expt(x:rat,     n:nat) HAS_TYPE rat
  nnrat_expt:   JUDGEMENT expt(x:nnrat,   n:nat) HAS_TYPE nnrat
  posrat_expt:  JUDGEMENT expt(x:posrat,  n:nat) HAS_TYPE posrat
  int_expt:     JUDGEMENT expt(x:int,     n:nat) HAS_TYPE int
  nat_expt:     JUDGEMENT expt(x:nat,     n:nat) HAS_TYPE nat
  posnat_expt:  JUDGEMENT expt(x:posnat,  n:nat) HAS_TYPE posnat

  ^(r: real, i:{i:int | r /= 0 OR i >= 0}): real
    = IF i >= 0 THEN expt(r, i) ELSE 1/expt(r, -i) ENDIF

  expt_pos: LEMMA px^i > 0

  expt_nonzero: LEMMA n0x^i /= 0

  nnreal_exp:  JUDGEMENT ^(x:nnreal,  i:int | x/=0 OR i>=0) HAS_TYPE nnreal
  posreal_exp: JUDGEMENT ^(x:posreal, i:int) HAS_TYPE posreal
  nzreal_exp:  JUDGEMENT ^(x:nzreal,  i:int) HAS_TYPE nzreal
  rat_exp:     JUDGEMENT ^(x:rat,     i:int | x/=0 OR i>=0) HAS_TYPE rat
  nnrat_exp:   JUDGEMENT ^(x:nnrat,   i:int | x/=0 OR i>=0) HAS_TYPE nnrat
  posrat_exp:  JUDGEMENT ^(x:posrat,  i:int) HAS_TYPE posrat
  int_exp:     JUDGEMENT ^(x:int, n:nat)    HAS_TYPE int
  nat_exp:     JUDGEMENT ^(x:nat, n:nat)    HAS_TYPE nat
  posint_exp:  JUDGEMENT ^(x:posint, n:nat) HAS_TYPE posint

  % Properties of expt

  expt_x0_aux: LEMMA expt(x, 0) = 1

  expt_x1_aux: LEMMA expt(x, 1) = x

  expt_1n_aux: LEMMA expt(1, n) = 1

  increasing_expt_aux: LEMMA expt(gt1x, m+2) > gt1x

  decreasing_expt_aux: LEMMA expt(lt1x, m+2) < lt1x

  expt_1_aux: LEMMA expt(px, n + 1) = 1 IFF px = 1

  expt_plus_aux: LEMMA expt(n0x, m + n) = expt(n0x, m) * expt(n0x, n)

  expt_minus_aux: LEMMA
    m >= n IMPLIES expt(n0x, m - n) = expt(n0x, m)/expt(n0x, n)

  expt_times_aux: LEMMA expt(n0x, m * n) = expt(expt(n0x, m), n)

  expt_divide_aux: LEMMA 1/expt(n0x, m * n) = expt(1/expt(n0x, m), n)

  both_sides_expt1_aux: LEMMA expt(px, m+1) = expt(px, n+1) IFF m = n OR px = 1

  both_sides_expt2_aux: LEMMA expt(px, pm) = expt(py, pm) IFF px = py

  both_sides_expt_pos_lt_aux: LEMMA
    expt(px, m+1) < expt(py, m+1) IFF px < py

  both_sides_expt_gt1_lt_aux: LEMMA
    expt(gt1x, m+1) < expt(gt1x, n+1) IFF m < n

  both_sides_expt_lt1_lt_aux: LEMMA
    expt(lt1x, m+1) < expt(lt1x, n+1) IFF n < m

  both_sides_expt_pos_le_aux: LEMMA
    expt(px, m+1) <= expt(py, m+1) IFF px <= py

  both_sides_expt_gt1_le_aux: LEMMA
    expt(gt1x, m+1) <= expt(gt1x, n+1) IFF m <= n

  both_sides_expt_lt1_le_aux: LEMMA
    expt(lt1x, m+1) <= expt(lt1x, n+1) IFF n <= m

  both_sides_expt_pos_gt_aux: LEMMA
    expt(px, m+1) > expt(py, m+1) IFF px > py

  both_sides_expt_gt1_gt_aux: LEMMA
    expt(gt1x, m+1) > expt(gt1x, n+1) IFF m > n

  both_sides_expt_lt1_gt_aux: LEMMA
    expt(lt1x, m+1) > expt(lt1x, n+1) IFF n > m

  both_sides_expt_pos_ge_aux: LEMMA
    expt(px, m+1) >= expt(py, m+1) IFF px >= py

  both_sides_expt_gt1_ge_aux: LEMMA
    expt(gt1x, m+1) >= expt(gt1x, n+1) IFF m >= n

  both_sides_expt_lt1_ge_aux: LEMMA
    expt(lt1x, m+1) >= expt(lt1x, n+1) IFF n >= m

  expt_of_mult: LEMMA expt(x * y, n) = expt(x, n) * expt(y, n)

  expt_of_div: LEMMA expt(x / n0y, n) = expt(x, n) / expt(n0y, n)

  expt_of_inv: LEMMA expt(1 / n0x, n) = 1 / expt(n0x, n)

  expt_of_abs: LEMMA expt(abs(x), n) = abs(expt(x, n))

  abs_of_expt_inv: LEMMA abs(1 / expt(n0x, n)) = 1 / expt(abs(n0x),n)

  % Properties of ^

  expt_x0: LEMMA x^0 = 1

  expt_x1: LEMMA x^1 = x
  expt_x2: LEMMA x^2 = x*x
  expt_x3: LEMMA x^3 = x*x*x
  expt_x4: LEMMA x^4 = x*x*x*x

  expt_1i: LEMMA 1^i = 1

  expt_eq_0: LEMMA x^pn = 0 IFF x = 0

  expt_plus: LEMMA n0x^(i + j) = n0x^i * n0x^j

  expt_times: LEMMA n0x^(i * j) = (n0x^i)^j

  expt_inverse: LEMMA n0x^(-i) = 1/(n0x^i)

  expt_div: LEMMA n0x^i/n0x^j = n0x^(i-j)

  both_sides_expt1: LEMMA px ^ n0i = px ^ n0j IFF n0i = n0j OR px = 1

  both_sides_expt2: LEMMA px ^ n0i = py ^ n0i IFF px = py

  b: VAR above(1)

  pos_expt_gt: LEMMA n < b^n

  expt_ge1: LEMMA b^n >= 1

  both_sides_expt_pos_lt: LEMMA px ^ pm < py ^ pm IFF px < py

  both_sides_expt_gt1_lt: LEMMA gt1x ^ i < gt1x ^ j IFF i < j

  both_sides_expt_lt1_lt: LEMMA lt1x ^ i < lt1x ^ j IFF j < i

  both_sides_expt_pos_le: LEMMA px ^ pm <= py ^ pm IFF px <= py

  both_sides_expt_gt1_le: LEMMA gt1x ^ i <= gt1x ^ j IFF i <= j

  both_sides_expt_lt1_le: LEMMA lt1x ^ i <= lt1x ^ j IFF j <= i

  both_sides_expt_pos_gt: LEMMA px ^ pm > py ^ pm IFF px > py

  both_sides_expt_gt1_gt: LEMMA gt1x ^ i > gt1x ^ j IFF i > j

  both_sides_expt_lt1_gt: LEMMA lt1x ^ i > lt1x ^ j IFF j > i

  both_sides_expt_pos_ge: LEMMA px ^ pm >= py ^ pm IFF px >= py

  both_sides_expt_gt1_ge: LEMMA gt1x ^ i >= gt1x ^ j IFF i >= j

  both_sides_expt_lt1_ge: LEMMA lt1x ^ i >= lt1x ^ j IFF j >= i

  expt_gt1_pos: LEMMA gt1x^pm >= gt1x

  expt_gt1_neg: LEMMA gt1x^(-pm) < 1

  expt_gt1_nonpos: LEMMA gt1x^(-m) <= 1

  mult_expt: LEMMA (n0x * n0y) ^i = n0x^i * n0y^i

  div_expt : LEMMA (n0x / n0y)^i = n0x^i / n0y^i

  inv_expt : LEMMA (1 / n0x)^i = 1 / n0x^i

  abs_expt: LEMMA abs(n0x)^i = abs(n0x^i)

  abs_hat_nat : LEMMA abs(x)^n = abs(x^n)

  expt_minus1_abs:   LEMMA abs((-1)^i) = 1
  even_m1_pow: LEMMA even?(i) IMPLIES (-1)^i = 1
  not_even_m1_pow: LEMMA NOT even?(i) IMPLIES (-1)^i = -1

  expt_lt1_bound1: LEMMA  expt(lt1x, n) <= 1

  expt_lt1_bound2: LEMMA  expt(lt1x, pn) < 1

  expt_gt1_bound1: LEMMA  1 <= expt(gt1x, n)

  expt_gt1_bound2: LEMMA  1 < expt(gt1x, pn)

  large_expt: LEMMA 1 < px  IMPLIES (FORALL py: EXISTS n: py < expt(px, n))

  small_expt: LEMMA px < 1  IMPLIES (FORALL py: EXISTS n: expt(px, n) < py)

  exponent_adjust: LEMMA b^i+b^(i-pm) < b^(i+1)

  exp_of_exists: lemma exists i: b^i <= py & py < b^(i+1)

 END exponentiation
```

####subrange

```
subrange_type[m, n: int] : THEORY
 BEGIN
  subrange: TYPE = subrange(m, n)
 END subrange_type


subrange_inductions[i: int, j: upfrom(i)]: THEORY
 BEGIN
  k, m: VAR subrange(i, j)            //i,i+1,....j
  p: VAR pred[subrange(i, j)]

  subrange_induction: LEMMA
    (p(i) AND (FORALL k: k < j AND p(k) IMPLIES p(k + 1)))
        IMPLIES (FORALL k: p(k))

  SUBRANGE_induction: LEMMA
    (FORALL k: (FORALL m: m < k IMPLIES p(m)) IMPLIES p(k))
      IMPLIES (FORALL k: p(k))

 END subrange_inductions
```

#### finite_sets

```
finite_sets[T: TYPE]: THEORY
% beginning of original finite_sets_def theory
 BEGIN
  x, y, z: VAR T
  s: VAR set[T]
  N: VAR nat

  is_finite(s): bool = (EXISTS N, (f: [(s) -> below[N]]):
                          injective?(f)) 

  finite_set: TYPE = (is_finite) CONTAINING emptyset[T]

  non_empty_finite_set: TYPE = {s: finite_set | NOT empty?(s)}

  is_finite_surj: LEMMA (EXISTS (N: nat), (f: [below[N] -> (s)]):
                            surjective?(f)) IFF is_finite(s) 


  A, B: VAR finite_set
  NA, NB: VAR non_empty_finite_set

  finite_subset:       LEMMA subset?(s,A) IMPLIES is_finite(s)
  finite_intersection: LEMMA is_finite(intersection(A,B))
  finite_add:          LEMMA is_finite(add(x,A))

  % -------- Judgements --------------------------------------------------

  nonempty_finite_is_nonempty: JUDGEMENT
    non_empty_finite_set SUBTYPE_OF (nonempty?[T])

  finite_singleton: JUDGEMENT singleton(x) HAS_TYPE finite_set

  finite_union: JUDGEMENT union(A, B) HAS_TYPE finite_set
  finite_intersection1: JUDGEMENT intersection(s, A) HAS_TYPE finite_set
  finite_intersection2: JUDGEMENT intersection(A, s) HAS_TYPE finite_set
  finite_difference: JUDGEMENT difference(A, s) HAS_TYPE finite_set

  nonempty_finite_union1: JUDGEMENT union(NA, B) HAS_TYPE non_empty_finite_set

  nonempty_finite_union2: JUDGEMENT union(A, NB) HAS_TYPE non_empty_finite_set

  nonempty_add_finite: JUDGEMENT add(x, A) HAS_TYPE non_empty_finite_set

  finite_remove: JUDGEMENT remove(x, A) HAS_TYPE finite_set

  finite_rest: JUDGEMENT rest(A) HAS_TYPE finite_set

  finite_emptyset: JUDGEMENT emptyset HAS_TYPE finite_set

  nonempty_singleton_finite: JUDGEMENT
   singleton(x) HAS_TYPE non_empty_finite_set


  % -------- Base type is finite -----------------------------------------

  is_finite_type: bool = (EXISTS N, (g:[T -> below[N]]): injective?(g))

  finite_full:         LEMMA is_finite_type IFF is_finite(fullset[T])
  finite_type_set:     LEMMA is_finite_type IMPLIES is_finite(s)
  finite_complement:   LEMMA is_finite_type IMPLIES is_finite(complement(s))

% end of original finite_sets_def

% beginning of original card_def theory

%------------------------------------------------------------------------
%
%  Fundamental definition of card
%
%      Authors:  Bruno Dutertre 
%                Ricky W. Butler
%------------------------------------------------------------------------

  S, S2: VAR finite_set
  n, m: VAR nat
  p : VAR posnat

  inj_set(S): (nonempty?[nat]) = 
                 { n | EXISTS (f : [(S)->below[n]]) : injective?(f) }

  Card(S): nat = min(inj_set(S))

  inj_Card    : LEMMA Card(S) = n IMPLIES 
                          (EXISTS (f : [(S) -> below[n]]) : injective?(f))

  reduce_inj  : LEMMA (FORALL (f : [(S)->below[p]]) :
                          injective?(f) AND NOT surjective?(f) IMPLIES 
                              (EXISTS (g: [(S)->below[p-1]]): injective?(g)))

  Card_injection: LEMMA (EXISTS (f : [(S)->below[n]]) : injective?(f)) 
                            IMPLIES Card(S) <= n
  
  Card_surjection : LEMMA (EXISTS (f : [(S)->below[n]]) : surjective?(f)) 
                              IMPLIES n <= Card(S) 
        
  Card_bijection : THEOREM
        Card(S) = n IFF (EXISTS (f : [(S)->below[n]]) : bijective?(f))

  Card_disj_union: THEOREM disjoint?(S,S2) IMPLIES 
                              Card(union(S,S2)) = Card(S) + Card(S2)

% ------------------------------------------------------------------------
  card(S): {n: nat| n = Card(S)}               % inhibit expansion

  card_def        : THEOREM card(S) = Card(S)  % But if you really need to

% end of original card_def theory

% beginning of original finite_sets theory 

  card_emptyset   : THEOREM card(emptyset[T]) = 0

  empty_card      : THEOREM empty?(S) IFF card(S) = 0

  card_empty?     : THEOREM (card(S) = 0) = empty?(S)

  card_is_0       : THEOREM (card(S) = 0) = (S = emptyset)

  nonempty_card   : THEOREM nonempty?(S) IFF card(S) > 0

  card_singleton  : THEOREM card(singleton(x)) = 1

  card_one        : THEOREM card(S) = 1 IFF (EXISTS x : S = singleton(x))

  card_disj_union : THEOREM disjoint?(A,B) IMPLIES 
                              card(union(A,B)) = card(A) + card(B)

  card_diff_subset: THEOREM subset?(A, B) IMPLIES 
                                  card(difference(B, A)) = card(B) - card(A)

  card_subset     : THEOREM subset?(A,B) IMPLIES card(A) <= card(B)

  card_plus       : THEOREM card(A) + card(B) = 
                                card(union(A, B)) + card(intersection(A,B))  

  card_union      : THEOREM card(union(A,B)) = card(A) + card(B) -
                                               card(intersection(A,B))

  card_add        : THEOREM card(add(x, S)) = card(S) 
                                                   + IF S(x) THEN 0 ELSE 1 ENDIF
  card_add_gt0    : THEOREM card(add(x,S)) > 0

  card_remove     : THEOREM card(remove(x, S)) = card(S)
                                                   - IF S(x) THEN 1 ELSE 0 ENDIF

  card_rest       : THEOREM NOT empty?(S) IMPLIES card(rest(S)) = card(S) - 1

  same_card_subset: THEOREM subset?(A, B) AND card(A) = card(B) 
                               IMPLIES A = B

  smaller_card_subset : THEOREM subset?(A, B) AND card(A) < card(B) IMPLIES
                                 (EXISTS x : member(x, B) AND NOT member(x, A))

  card_strict_subset: THEOREM strict_subset?(A,B) IMPLIES card(A) < card(B)

  card_1_has_1    : THEOREM card(S) >= 1 IMPLIES (EXISTS (x: T): S(x))

  card_2_has_2    : THEOREM card(S) >= 2 IMPLIES
                              (EXISTS (x,y: T): x /= y AND S(x) AND S(y))

  card_intersection_le: THEOREM card(intersection(A,B)) <= card(A) AND
                              card(intersection(A,B)) <= card(B)

  card_bij        : THEOREM card(S) = N
                             IFF (EXISTS (f: [(S) -> below[N]]): bijective?(f))

  card_bij_inv    : THEOREM card(S) = N
                             IFF (EXISTS (f: [below[N] -> (S)]): bijective?(f))

  bij_exists      : COROLLARY (EXISTS (f: [(S) -> below(card(S))]):
                                       bijective?(f))

  bij(S: finite_set): {f:[(S) -> below(card(S))] | bijective?(f)}

  ibij(S: non_empty_finite_set): {f:[below(card(S)) -> (S)] | bijective?(f)} =
       inverse(bij(S))

  bij_ibij: LEMMA FORALL (S: non_empty_finite_set, ii: below(card(S))):
      bij(S)(ibij(S)(ii)) = ii

  ibij_bij: LEMMA FORALL (S: non_empty_finite_set, x:T):
      S(x) IMPLIES ibij(S)(bij(S)(x)) = x

  is_finite_exists_N: LEMMA FORALL (g: [below[N] -> T]):
            is_finite({r: T | EXISTS (n: below[N]): r = g(n)})

%  card_n_has_n    : THEOREM card(S) >= n IMPLIES
%                              (EXISTS (X: [below[N] -> T]):
%                                   (FORALL (i: below[N]): S(X(i))) AND
%                                   (FORALL (i,j: below[N]): X(i) /= X(j)))

  P, P1, P2: VAR pred[T]

  finite_pred: LEMMA is_finite(fullset[T]) IMPLIES
                        is_finite[T]({x: T | P(x)})

  finite_pred2:  LEMMA is_finite(P) IMPLIES
                         is_finite[T]({x: T | P(x)})

  card_implies: LEMMA is_finite(fullset[T]) AND
                      (FORALL (x: T): P1(x) IMPLIES P2(x)) 
             IMPLIES card({x: T | P1(x)}) <= card({x: T | P2(x)})

  finite_induction: THEOREM
    FORALL (p: pred[set[T]]):
      (FORALL (n: nat), (S: set[T]):
         (EXISTS (f: [(S) -> below[n]]): injective?(f)) => p(S))
       => (FORALL (FS: finite_set): p(FS))
               
END finite_sets
```

#### function_iterate

```
function_iterate[T: TYPE]: THEORY
 BEGIN
  f: VAR [T -> T]
  m, n: VAR nat
  x: VAR T

  iterate(f, n)(x): RECURSIVE T =
    IF n = 0 THEN x ELSE f(iterate(f, n-1)(x)) ENDIF
    MEASURE n

  iterate_add: LEMMA
    iterate(f, m) o iterate(f, n) = iterate(f, m + n)

  iterate_add_applied: LEMMA 
    iterate(f,m)(iterate(f, n)(x)) = iterate(f, m + n)(x)

  iterate_add_one: LEMMA 
    iterate(f, n)(f(x)) = iterate(f, n+1)(x)

  iterate_mult: LEMMA
    iterate(iterate(f, m), n) = iterate(f, m * n)

  iterate_invariant: LEMMA
    f(iterate(f, n)(x)) = iterate(f, n)(f(x))
  
 END function_iterate

```

####sequences

```
sequences[T: TYPE]: THEORY
 BEGIN

  sequence: TYPE = [nat->T]
  i, n: VAR nat
  x: VAR T
  p: VAR pred[T]
  seq: VAR sequence
  Trel: VAR PRED[[T, T]]

  nth(seq, n): T = seq(n)

  suffix(seq, n): sequence =
    (LAMBDA i: seq(i+n))

  first(seq): T = nth(seq, 0)

  rest(seq): sequence = suffix(seq, 1)

  delete(n, seq): sequence =
    (LAMBDA i: (IF i < n THEN seq(i) ELSE seq(i + 1) ENDIF))

  insert(x, n, seq): sequence =
    (LAMBDA i: (IF i < n THEN seq(i)
                ELSIF i = n THEN x
                ELSE seq(i - 1) ENDIF))

  add(x, seq): sequence = insert(x, 0, seq)

  insert_delete: LEMMA insert(nth(seq, n), n, delete(n, seq)) = seq

  add_first_rest: LEMMA add(first(seq), rest(seq)) = seq

  every(p)(seq): bool = (FORALL n: p(nth(seq, n)))

  every(p, seq): bool = (FORALL n: p(nth(seq, n)))

  some(p)(seq): bool = (EXISTS n: p(nth(seq, n)))

  some(p, seq): bool = (EXISTS n: p(nth(seq, n)))

  sequence_induction: LEMMA
    p(nth(seq, 0)) AND (FORALL n: p(nth(seq, n)) IMPLIES p(nth(seq, n + 1)))
      IMPLIES every(p)(seq)

  ascends?(seq, Trel): bool = preserves(seq, (LAMBDA i, n: i <= n), Trel)

  descends?(seq, Trel): bool = inverts(seq, (LAMBDA i, n: i <= n), Trel)

 END sequences
```

#### finite_sequences

```
finite_sequences [T: TYPE]: THEORY
 BEGIN
  finite_sequence: TYPE = [# length: nat, seq: [below[length] -> T] #]
  finseq: TYPE = finite_sequence

  fs, fs1, fs2, fs3: VAR finseq
  m, n: VAR nat

  empty_seq: finseq =
    (# length := 0,
       seq := (LAMBDA (x: below[0]): epsilon! (t:T): true) #)

  finseq_appl(fs): [below[length(fs)] -> T] = fs`seq;

  CONVERSION finseq_appl;

  o(fs1, fs2): finseq =
     LET l1 = fs1`length,
         lsum = l1 + fs2`length
      IN (# length := lsum,
            seq := (LAMBDA (n:below[lsum]):
                      IF n < l1
                         THEN fs1`seq(n)
                         ELSE fs2`seq(n-l1)
                      ENDIF) #);

  p: VAR [nat, nat]

  ^(fs, p): finseq =
    LET (m, n) = p
     IN IF m > n OR m >= fs`length
        THEN empty_seq
        ELSE LET len = min(n - m + 1, fs`length - m)
              IN (# length := len,
                    seq := (LAMBDA (x: below[len]): fs`seq(x + m)) #)
        ENDIF;

  % This is the same as above, but returns domain [m..n-1] rather than [m..n]
  % More generally useful, but the above is kept for backward compatibility
  ^^(fs, p): finseq =
    LET (m, n) = p
     IN IF m >= n OR m >= fs`length
        THEN empty_seq
        ELSE LET len = min(n - m, fs`length - m)
              IN (# length := len,
                    seq := (LAMBDA (x: below[len]): fs`seq(x + m)) #)
        ENDIF

  extract1(fs:{fs | fs`length = 1}): T = fs`seq(0)

  CONVERSION extract1

  o_assoc: LEMMA fs1 o (fs2 o fs3) = (fs1 o fs2) o fs3

 END finite_sequences
```

####more_finseq

```
more_finseq [T: TYPE]: THEORY
  BEGIN
   seq: TYPE = finseq[T]

   rr, ss, tt: VAR seq

   x, y, z: VAR T

   prefix?(rr, ss): bool =
     rr`length <= ss`length AND
       (FORALL (i: below(rr`length)): rr`seq(i) = ss`seq(i))

   prefix_closed?(X : set[seq]): bool =
     FORALL ss: X(ss) IMPLIES (FORALL (rr | prefix?(rr, ss)): X(rr))

   add(x, rr): finseq[T] =
     rr WITH [`length := rr`length + 1,
              `seq(rr`length) := x]

  END more_finseq
```

### datatype

#### ordstruct

```
ordstruct: DATATYPE
 BEGIN
  zero: zero?
  add(coef: posnat, exp: ordstruct, rest: ordstruct): nonzero?
 END ordstruct


% ordinals uses the ordstruct datatype to define the ordinals up to
% epsilon_0, by providing an ordering on ordstruct and defining the
% ordinals to be those elements of type ordstruct that are in a
% Cantor normal form with respect to the ordering.  Thus
%    add(i, u, v) = (omega^u)*i + v
%    add(i, u, add(j, v, w)) = (omega^u)*i + (omega^v)*j + w, where u>v.  

ordinals: THEORY
 BEGIN
  i, j, k: VAR posnat
  m, n, o: VAR nat
  u, v, w, x, y, z: VAR ordstruct
  size: [ordstruct->nat] = reduce[nat](0, (LAMBDA i, m, n: 1 + m+n));

  <(x, y): RECURSIVE bool =
     CASES x OF
        zero: NOT zero?(y),
        add(i, u, v): CASES y OF
                        zero: FALSE,
                        add(j, z, w): (u<z) OR
                                      (u=z) AND (i<j) OR
                                      (u=z) AND (i=j) AND (v<w)
                      ENDCASES
     ENDCASES
   MEASURE size(x);

  >(x, y): bool = y < x;
  <=(x, y): bool = x < y OR x = y;
  >=(x, y): bool = y < x OR y = x

  ordinal?(x): RECURSIVE bool =
    CASES x OF
      zero: TRUE,
      add(i, u, v): (ordinal?(u) AND ordinal?(v) AND 
                      CASES v OF
                        zero: TRUE,
                        add(k, r, s): r < u
                      ENDCASES)
    ENDCASES
   MEASURE size

  ordinal: NONEMPTY_TYPE = (ordinal?)

  r, s, t: VAR ordinal
  
  ordinal_irreflexive: LEMMA NOT r < r

  ordinal_antisym: LEMMA r < s IMPLIES NOT s < r

  ordinal_antisymmetric: LEMMA r <= s AND s <= r IMPLIES r = s

  ordinal_transitive: LEMMA r < s AND s < t IMPLIES r < t

  ordinal_trichotomy: LEMMA r < s OR r = s OR s < r

  p: VAR pred[ordinal]

  ordinal_induction: AXIOM
    (FORALL r: (FORALL s: s < r IMPLIES p(s)) IMPLIES p(r))
      IMPLIES (FORALL r: p(r))

  well_founded_le: LEMMA
    well_founded?(LAMBDA (r, s: (ordinal?)): r < s)

 END ordinals
```

#### lex2字典序

```
lex2: THEORY
  BEGIN

  i, j, m, n: VAR nat

  lex2(m, n): ordinal = 
    (IF m=0
       THEN IF n = 0
               THEN zero
               ELSE add(n, zero, zero)
            ENDIF
       ELSIF n = 0 THEN add(m, add(1,zero,zero),zero)
       ELSE add(m, add(1,zero,zero), add(n,zero, zero))
     ENDIF)

  lex2_lt: LEMMA
    lex2(i, j) < lex2(m, n) =
     (i < m OR (i = m AND j < n))

 END lex2
```

#### list 

```
list [T: TYPE]: DATATYPE 
 BEGIN
  null: null?
  cons (car: T, cdr:list):cons?

 END list
```

```
list_props [T: TYPE]: THEORY
 BEGIN
  l, l1, l2, l3: VAR list[T]
  x: VAR T
  P, Q: VAR PRED[T]

  length(l): RECURSIVE nat =
    CASES l OF
      null: 0,
      cons(x, y): length(y) + 1
    ENDCASES
   MEASURE reduce_nat(0, (LAMBDA (x: T), (n: nat): n + 1))

  member(x, l): RECURSIVE bool =
    CASES l OF
      null: FALSE,
      cons(hd, tl): x = hd OR member(x, tl)
    ENDCASES
   MEASURE length(l)

  member_null: LEMMA member(x, l) IMPLIES NOT null?(l)

  nth(l, (n:below[length(l)])): RECURSIVE T =
    IF n = 0 THEN car(l) ELSE nth(cdr(l), n-1) ENDIF
   MEASURE length(l)

  append(l1, l2): RECURSIVE list[T] =
    CASES l1 OF
      null: l2,
      cons(x, y): cons(x, append(y, l2))
    ENDCASES
    MEASURE length(l1)

  reverse(l): RECURSIVE list[T] =
    CASES l OF
      null: l,
      cons(x, y): append(reverse(y), cons(x, null))
    ENDCASES
    MEASURE length

  append_null: LEMMA append(l, null) = l

  append_assoc: LEMMA
     append(append(l1, l2), l3) = append(l1, append(l2, l3))

  reverse_append: LEMMA
    reverse(append(l1, l2)) = append(reverse(l2), reverse(l1))

  reverse_reverse: LEMMA reverse(reverse(l)) = l

  length_append: LEMMA length(append(l1, l2)) = length(l1) + length(l2)

  length_reverse: LEMMA length(reverse(l)) = length(l)

  a, b, c: VAR T

  list_rep: LEMMA (: a, b, c :) = cons(a, cons(b, cons(c, null)))

  every_append: LEMMA
    every(P)(append(l1, l2)) IFF (every(P)(l1) AND every(P)(l2))

  every_disjunct1: LEMMA
    every(P)(l) IMPLIES every(LAMBDA (x: T): P(x) OR Q(x))(l)

  every_disjunct2: LEMMA
    every(Q)(l) IMPLIES every(LAMBDA (x: T): P(x) OR Q(x))(l)

  every_conjunct: LEMMA
    every(LAMBDA (x: T): P(x) AND Q(x))(l) => (every(P)(l) AND every(Q)(l))

  every_conjunct2: LEMMA
    (every(P)(l) AND every(Q)(l)) => every(LAMBDA (x: T): P(x) AND Q(x))(l)

  every_member: LEMMA every({c: T | member(c, l)})(l)

  every_nth: LEMMA
      every(P)(l) IFF FORALL (i:below(length(l))): P(nth(l,i))

 END list_props
```

#### filters

```
filters[T: TYPE]: THEORY
 BEGIN
  s: VAR set[T]
  l: VAR list[T]
  p: VAR pred[T]
  
  filter(s, p): set[T] = {x: T | s(x) & p(x)}

  filter(p)(s): set[T] = {x: T | s(x) & p(x)}

  filter(l, p): RECURSIVE list[T] =
    CASES l OF
      null: null,
      cons(x, y): IF p(x) THEN cons(x, filter(y, p)) ELSE filter(y, p) ENDIF
    ENDCASES
    MEASURE length(l)

  filter(p)(l): RECURSIVE list[T] =
    CASES l OF
      null: null,
      cons(x, y): IF p(x) THEN cons(x, filter(p)(y)) ELSE filter(p)(y) ENDIF
    ENDCASES
    MEASURE length(l)
 END filters
```

#### list2finseq

```
list2finseq[T: TYPE]: THEORY
 BEGIN
  l: VAR list[T]
  fs: VAR finseq[T]
  n: VAR nat

  list2finseq(l): finseq[T] =
    (# length := length(l),
       seq := (LAMBDA (x: below[length(l)]): nth(l, x)) #)

  finseq2list_rec(fs, (n: nat | n <= length(fs))): RECURSIVE list[T] =
    IF n = 0
       THEN null
       ELSE cons(fs`seq(length(fs) - n),
                 finseq2list_rec(fs, n-1))
    ENDIF
    MEASURE n

  finseq2list(fs): list[T] = finseq2list_rec(fs, length(fs))

  CONVERSION list2finseq, finseq2list

 END list2finseq
```

#### list2set

```
list2set[T:TYPE]: THEORY
 BEGIN
  l: VAR list[T]
  x: VAR T

  list2set(l) : RECURSIVE set[T] =
    CASES l OF
      null: emptyset[T],
      cons(x, y): add(x, list2set(y))
    ENDCASES
   MEASURE length

  CONVERSION list2set

 END list2set

```

#### disjointness

```
disjointness: THEORY
 BEGIN
  l: VAR list[bool]
  pairwise_disjoint?(l): RECURSIVE boolean =
    cases l of
      null: true,
      cons(x,y): every(LAMBDA (z:bool): NOT (x AND z))(y)
                  AND pairwise_disjoint?(y)
    endcases
   MEASURE length(l)
 END disjointness
```

#### strings

```
strings: THEORY
 BEGIN
  char: TYPE = (char?)
  string: TYPE = finite_sequence[char]

  l1, l2: VAR list[char]

  c1, c2: VAR char

  fseq_lem: LEMMA
    (list2finseq(l1) = list2finseq(l2)) = (l1 = l2)

  cons_lem: LEMMA (cons(c1,l1) = cons(c2,l2)) = (c1 = c2 & l1 = l2)

  char_lem: LEMMA (c1 = c2) = (code(c1) = code(c2))
 END strings
```

#### mucalculus

```
mucalculus[T:TYPE]: THEORY
 BEGIN

  s: VAR T
  p, p1, p2: VAR pred[T]
  predicate_transformer: TYPE = [pred[T]->pred[T]]
  pt: VAR predicate_transformer
  setofpred: VAR pred[pred[T]]

  <=(p1,p2): bool = FORALL s: p1(s) IMPLIES p2(s)

  monotonic?(pt): bool =
      FORALL p1, p2: p1 <= p2 IMPLIES pt(p1) <= pt(p2)

  pp: VAR (monotonic?)

  fixpoint?(pp, p): bool = (pp(p) = p)

  fixpoint?(pp)(p): bool = fixpoint?(pp, p)

  glb(setofpred): pred[T] =
      LAMBDA s: (FORALL p: member(p,setofpred) IMPLIES p(s))

  % least fixpoint
  lfp(pp): pred[T] = glb({p | pp(p) <= p})

  lfp_induction: FORMULA pp(p) <= p IMPLIES lfp(pp) <= p

  mu(pp): pred[T] = lfp(pp)

  lfp?(pp, p1): bool =
    fixpoint?(pp, p1) AND FORALL p2: fixpoint?(pp,p2) IMPLIES p1 <= p2

  lfp?(pp)(p1): bool = lfp?(pp, p1)

  lub(setofpred): pred[T] = LAMBDA s: EXISTS p: member(p,setofpred) AND p(s)

  % greatest fixpoint
  gfp(pp): pred[T] = lub({p | p <= (pp(p))})

  gfp_induction: FORMULA p <= pp(p) IMPLIES p <= gfp(pp)

  nu(pp): pred[T] = gfp(pp)

  gfp?(pp, p1): bool =
    fixpoint?(pp,p1) AND FORALL p2: fixpoint?(pp,p2) IMPLIES p2 <= p1

  gfp?(pp)(p1): bool = gfp?(pp, p1)

 END mucalculus
```

####CTLops

```
ctlops[state : TYPE]: THEORY
 BEGIN
  u,v,w: VAR state
  f,g,Q,P,p1,p2: VAR pred[state]
  Z: VAR pred[[state, state]]

  N: VAR [state, state -> bool]

  CONVERSION+ K_conversion

  EX(N,f)(u):bool = (EXISTS v: (f(v) AND N(u, v)))

  EG(N,f):pred[state] = nu(LAMBDA Q: (f AND EX(N,Q)))

  EU(N,f,g):pred[state] = mu(LAMBDA Q: (g OR (f AND EX(N,Q))))

  % Other operator definitions
  EF(N,f):pred[state] = EU(N, TRUE, f)
  AX(N,f):pred[state] = NOT EX(N, NOT f)
  AF(N,f):pred[state] = NOT EG(N, NOT f)
  AG(N,f):pred[state] = NOT EF(N, NOT f)
  AU(N,f,g):pred[state] = NOT EU(N, NOT g, (NOT f AND NOT g)) AND AF(N, g)

  CONVERSION- K_conversion
 END ctlops
```

####fairCTLops

```
fairctlops  [ state : TYPE ]: THEORY
 BEGIN
  u,v,w: VAR state
  f,g,Q,P,p1,p2: VAR pred[state]
  N: VAR [state, state->bool]
  Ff: VAR pred[state] % Fair formula

  CONVERSION+ K_conversion

  fairEG(N, f)(Ff): pred[state] =
    nu(LAMBDA P: EU(N, f, f AND Ff AND EX(N, P)))

  fairAF(N,f)(Ff):pred[state]  = NOT fairEG(N, NOT f)(Ff)

  fair?(N,Ff):pred[state] = fairEG(N,LAMBDA u: TRUE)(Ff)

  fairEX(N,f)(Ff):pred[state] = EX(N,f AND fair?(N,Ff))

  fairEU(N,f,g)(Ff):pred[state] = EU(N,f,g AND fair?(N,Ff))

  fairEF(N,f)(Ff):pred[state] = EF(N,f AND fair?(N,Ff))

  fairAX(N,f)(Ff):pred[state] = NOT fairEX(N, NOT f)(Ff)

  fairAG(N,f)(Ff):pred[state] = NOT fairEF(N,NOT f)(Ff)

  fairAU(N,f,g)(Ff):pred[state] =
	NOT fairEU(N,NOT g,NOT f AND NOT g)(Ff)
	    AND fairAF(N,g)(Ff)

  CONVERSION- K_conversion

 END fairctlops
```

#### FairCTLops

```
% Fair versions of CTL operators with lists of fairness conditions.
% FairAG(N,f)(Fflist)(s) means f always holds on every N-path from
% s along which each predicate in Fflist holds infinitely often.

Fairctlops[state : TYPE]: THEORY
 BEGIN
  u,v,w: VAR state
  f,g,Q,P,p1,p2: VAR pred[state]
  N: VAR [state, state->bool]
  Fflist, Gflist: VAR list[pred[state]] % Fair formula

  CONVERSION+ K_conversion

  CheckFair(Q, N, f, Fflist): RECURSIVE pred[state] =
    (CASES Fflist OF
     cons(Ff, Gflist):
	     EU(N, f, f AND Ff AND
		      EX(N, CheckFair(Q, N, f, Gflist))),
     null : Q
     ENDCASES)
    MEASURE length(Fflist)

  FairEG(N, f)(Fflist): pred[state] =
    nu(LAMBDA P: CheckFair(P, N, f, Fflist))

  FairAF(N,f)(Fflist):pred[state]  = NOT FairEG(N, NOT f)(Fflist)

  Fair?(N,Fflist):pred[state] = FairEG(N,LAMBDA u: TRUE)(Fflist)

  FairEX(N,f)(Fflist):pred[state] = EX(N,f AND Fair?(N,Fflist))

  FairEU(N,f,g)(Fflist):pred[state] = EU(N,f,g AND Fair?(N,Fflist))

  FairEF(N,f)(Fflist):pred[state] = EF(N,f AND Fair?(N,Fflist))

  FairAX(N,f)(Fflist):pred[state] = NOT FairEX(N, NOT f)(Fflist)

  FairAG(N,f)(Fflist):pred[state] = NOT FairEF(N,NOT f)(Fflist)

  FairAU(N,f,g)(Fflist):pred[state] =
	NOT FairEU(N,NOT g,NOT f AND NOT g)(Fflist)
	    AND FairAF(N,g)(Fflist)

  % Turn off K_conversion so that it is not a conversion by default.
  CONVERSION- K_conversion
 END Fairctlops
```



### modal logic



#### eg_direct

```
eg_direct: THEORY
BEGIN

  worlds: TYPE+
  pmlformulas: TYPE = [worlds -> bool]
  
  pvars: TYPE+
  
  v, w: VAR worlds
  x: VAR pvars
  val(x)(w): bool
  g: pvars
  P: VAR pmlformulas
  
  H1: AXIOM EXISTS w: val(g)(w)
  H2: AXIOM P(w) IMPLIES FORALL v: P(v)
  HC: THEOREM val(g)(w)
  
END eg_direct
```

#### elem_shallow_pml

```
elem_shallow_pml: THEORY
BEGIN
    worlds: TYPE+
    pmlformulas: TYPE = [worlds -> bool]
    
    pvars: TYPE+
    v, w: VAR worlds
    x, y: VAR pvars
    P, Q: VAR pmlformulas
    
    val(x)(w): bool
    
    fei(P)(w): bool = NOT P(w) ;
    &(P, Q)(w): bool = P(w) AND Q(w) ;
    =>(P, Q)(w): bool = P(w) IMPLIES Q(w) ;
    G(P)(w): bool = FORALL v: P(v) ;
    F(P): pmlformulas = fei( G( fei( P))) ;


    |=(w, P): bool = P(w)
    valid(P): bool = FORALL w: w |= P
    
END elem_shallow_pml
```

####eg_elem_shallow1

```
eg_elem_shallow1: THEORY
BEGIN IMPORTING elem_shallow_pml

    g: pvars
    P: var pmlformulas
    H1: AXIOM valid(F(val(g)))
    H2: AXIOM valid(P => G(P))
    HC: THEOREM valid(val(g))
    
END eg_elem_shallow1
```

#### eg_elem_shallow2

```
eg_elem_shallow2: THEORY

BEGIN IMPORTING elem_shallow_pml

    g: pvars
    P: var pmlformulas
    
    validval(x: pvars): bool = valid(val(x))
    CONVERSION valid, val, validval
    
    H1: AXIOM F(g)
    H2: AXIOM P => G(P)
    HC: THEOREM g
END eg_elem_shallow2

```



#### modalformula

```
modalformula[pvars: TYPE+]: DATATYPE
BEGIN

    pvar(arg: pvars): var?
    fei(arg: modalformula): not?
    &(arg1: modalformula, arg2: modalformula): and?
    =>(arg1: modalformula, arg2: modalformula): imp?	
    G(arg: modalformula): always?

END modalformula

```

#### elem_deep_pml

```
elem_deep_pml: THEORY
BEGIN

    worlds, pvars: TYPE+
    IMPORTING modalformula[pvars]
    w, v: VAR worlds
    x, y: VAR pvars
    P, Q: VAR modalformula
    val(x)(w): bool
    |=(w, P): RECURSIVE bool =
    	  CASES P OF
	  pvar(x): val(x)(w),
	  fei(R): NOT (w |= R),
	  &(R, S): (w |= R) AND (w |= S),
	  =>(R, S): (w |= R) IMPLIES (w |= S),
	  G(R): FORALL v: (v |= R)
	  ENDCASES
	  MEASURE P by <<

    F(P): modalformula = fei(G(fei(P))) ;
    valid(P): bool = FORALL w: w |= P
    validval(x: pvars): bool = valid(pvar(x))
    CONVERSION valid, pvar, validval


END elem_deep_pml

```

####eg_elem_deep

```
eg_elem_deep: THEORY
BEGIN IMPORTING elem_deep_pml

    g: pvars
    P: VAR modalformula
    
    H1: AXIOM F(g)
    H2: AXIOM P => G(P)
    HC: THEOREM g

END eg_elem_deep
```

#### full_shallow_pm

```
full_shallow_pml [worlds: TYPE+, access: pred[[worlds,worlds]],
		 pvars: TYPE+, val: [pvars -> [worlds -> bool]]]: THEORY
    BEGIN
    
    pmlformulas: TYPE = [worlds -> bool]
    v, w: VAR worlds
    x, y: VAR pvars
    P, Q: VAR pmlformulas
    
    fei(P)(w): bool = NOT P(w) ;
    &(P, Q)(w): bool = P(w) AND Q(w) ;
    =>(P, Q)(w): bool = P(w) IMPLIES Q(w) ;
    G (P)(w): bool = FORALL v: access(w, v) IMPLIES P(v) ;
    F(P)(w): bool = EXISTS v: access(w, v) AND P(v) ;
    
    |=(w, P): bool = P(w)
    valid(P): bool = FORALL w: w |= P
    validval(x: pvars): bool = valid(val(x))
    CONVERSION valid, val, validval
		
END full_shallow_pml


eg_full_shallow: THEORY
BEGIN

    worlds, pvars: TYPE+
    access: pred[[worlds, worlds]]
    val(x:pvars)(w:worlds): bool
    IMPORTING full_shallow_pml[worlds, access, pvars, val]
    g: pvars
    P: var pmlformulas
    H1: AXIOM <> g
    H2: AXIOM P => G(P)
    HC: THEOREM symmetric?(access) => g

END eg_full_shallow

```

