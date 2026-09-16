# UML 
17.6.3.14
Critical Region
The interactionOperator critical designates that the CombinedFragment represents a critical region. A critical region 
means that the traces of the region cannot be interleaved by other OccurrenceSpecifications (on those Lifelines covered 
by the region). This means that the region is treated atomically by the enclosing fragment when determining the set of 
valid traces. Even though enclosing CombinedFragments may imply that some OccurrenceSpecifications may 
interleave into the region, such as with par-operator, this is prevented by defining a region.
Thus the set of traces of enclosing constructs are restricted by critical regions

interactionOperator（相互作用演算子）の「critical」は、そのCombinedFragment（複合フラグメント）がクリティカル領域（critical region）を表すことを指定します。クリティカル領域とは、その領域内のトレース（実行経路）に対し、他のOccurrenceSpecification（発生仕様）が割り込む（インターリーブする）ことができない領域を指します（ただし、対象となるのはその領域に包含されるLifeline上の要素に限ります）。つまり、有効なトレースの集合を決定する際、その領域は包含するフラグメントによってアトミック（不可分）なものとして扱われます。たとえ包含側のCombinedFragment（例えばpar演算子など）が、特定のOccurrenceSpecificationをその領域内に割り込ませるような構造を示唆していたとしても、クリティカル領域として定義することで、そのような割り込みは阻止されます。
このように、包含する構成要素のトレース集合は、クリティカル領域によって制限を受けることになります。
