## JS中的算法与数据结构——集合(Set)

集合(Set)

同数学中所学的一样，集合(Set)是由一组无序但彼此之间又有一定关系性的成员构成，每个成员在集合中只能出现一次，不同于我们之前说的字典，链表之类的，它是一种包含了不同元素的数据结构(集合中的元素称为成员)，从其定义中我们可以看出它具有两个很重要的特征：首先，集合中的成员是无序的，其次，集合中的成员是不相同的，即集合中不存在相同的成员。

实际上，很多编程语言中，集合并不是一种数据类型，但是如果你需要创建一个数据结构用来保存一些独一无二的元素时，集合就变得很有用了，接下来我们一起来看看JS中如何实现一个集合。

集合的定义

我们要实现一个集合，首先要对其一些定义做了解

不包含任何成员的集合称为空集，包含一切可能成员的集合称为全集。
如果两个集合里的成员都完全相同，则称两个集合相等。
如果一个集合所有成员都包含于另一个集合，则前一集合称为后一集合的一个子集。
集合的操作

通常来说，集合的基本操作有以下三种：

并集：将两个集合中的成员进行合并，得到一个新的集合
交集：将两个集合中共同存在的成员组成的一个新的集合
补集：属于一个集合而不属于另一个集合的成员组成的新的集合
集合的实现

集合（Set）的实现我们这里基于数组，用数组来存储数据，根据我们之前学习的以及上面提到的一些方法，我们可以将集合的构造函数定义如下（为了区别ES6的 set 类型，我们这里选择用 MySet 命名）：

//构造函数

function MySet () {
    this.dataStore = [];            // 数据存储
    this.add = add;                 // 添加成员
    this.remove = remove;           // 删除成员
    this.size = size;               // 集合元素个数
    this.union = union;             // 集合求并集
    this.intersect = intersect;     // 集合求交集
    this.subset = subset;           // 判断一个集合是否是另一集合的子集
    this.difference = difference;   // 集合求补集
    this.contains = contains;       // 判断某成员是否属于该集合
    this.show = show;               // 显示当前集合
}
我们第一个要实现的方法就是向集合中添加一个成员，即 add 方法

add：向集合中添加一个成员

//添加元素

function add (data) {
    //判断元素是否存在集合当中
    if( this.dataStore.indexOf( data )  < 0 ){
        this.dataStore.push(data);
        return true;
    }else{
        console.warn( 'Can not add ' + data + ', must already be in set');
        return false;
    }
}
我们之前提到，集合中的元素是独一无二的，因此，我们在将数据存储到数组之前，首先就是要确保该集合不存在该数据，因此，我们先用 indexOf 方法检查新加入的元素是否存在，如果找到了就返回该成员在数组中的位置；否则，就返回 -1 ，那么对应的 add 方法就可以定义返回布尔值，添加成功我们返回 true ， 否则返回 false ，这样就可以明确告诉我们是否正确的插入了一个元素。

remove：删除集合中某个成员

//删除元素

function remove (data) {
    //判断元素是否存在集合当中
    var pos = this.dataStore.indexOf(data);
    if( pos > -1 ){
        this.dataStore.splice(pos,1);
        return true;
    }else{
        console.warn( data + ' is not in set');
        return false;
    }
}
这里，我们顺理成章的实现了删除方法，它跟 add 方法很类似，首先要检查待删除元素是否存在于数组中，如果存在，我们调用数组的 splice() 方法删除该元素并返回 true ，否则，直接返回 false ，表示集合中不存在该元素。

现在，我们可以完成集合的添加和删除，要测试这些方法之前，我们首先得定义 show 方法，该方法用来显示集合中的成员，该方法的实现很简答，只需返回我们定义的数组即可：

show：显示集合中的成员

// 显示集合成员

function show(){
    console.log(this.dataStore);
    return this.dataStore;
}
接着，我们这会来测试一下：

var fruits = new MySet();

// 添加成员
fruits.add('Apple');
fruits.add('Banana');
fruits.add('Pear');
fruits.show();              // ["Apple", "Banana", "Pear"]

// 添加重复成员
fruits.add('Apple');        // Can not add Apple, must already be in set

// 删除成员
fruits.remove('Banana');    
fruits.show();              // ["Apple", "Pear"]

// 删除不存在的成员
fruits.remove('Banana');    // Banana is not in set
嗯，一切正常，我们可以来实现集合的一些高级操作了，我们先来看看 union （并集）的实现。

union：求集合并集

求集合的并集，就是要将两个集合合并成一个，并除去重复的元素，我们实现思路就是将第一个集合成员放到一个临时集合中，判断第二个集合的成员是否也属于第一个集合，如果为真，代表为重复元素，我们直接跳过该成员，否则将该成员加入临时集合，最后返回该集合即可；

那么，问题来了，我们要如何判断一个成员是否存在于该集合中？因此，我们需要一个辅助方法 contains()，它的实现也非常简单，直接用 indexOf 判断即可

//判断元素是否属于该集合

function contains (data) {
    if( this.dataStore.indexOf(data) > -1 ){
        return true;
    }else{
        return false;
    }
}
现在，我们可以定义 union 方法了

//求集合的并集

function union ( set ) {
    var tempSet = new MySet();
    for( var i = 0 ; i < this.dataStore.length ; i++ ){
        tempSet.add(this.dataStore[i]);
    }
    for( var i = 0 ; i< set.dataStore.length ; i++ ){
        if( !tempSet.contains(set.dataStore[i])){
            tempSet.dataStore.push(set.dataStore[i]);
        }
    }
    return tempSet;
}
这样，我们就可以就集合的并集了，

var fruits1 = new MySet();
fruits1.add('Apple');
fruits1.add('Banana');
fruits1.add('Pear');

var fruits2 = new MySet();
fruits2.add('Grape');
fruits2.add('Banana');
fruits2.add('Pear');
fruits2.add('Orange');

var union = fruits1.union( fruits2 );
union.show();                           // ["Apple", "Banana", "Pear", "Grape", "Orange"]
成功了！我们可以来看看求集合的交集了。

intersect：求集合的交集

有了上面求并集的思路，那么交集的定义来说也相对简单，思路就是发现第一个集合的成员也属于第二个集合时，就将该成员加入到新的集合，最后返回新的集合即可；

//求集合的交集

function intersect (set) {
    var tempSet = new MySet();
    for(var i = 0 ; i < this.dataStore.length ; i++ ){
        if( set.contains(this.dataStore[i])){
            tempSet.add(this.dataStore[i]);
        }
    }
    return tempSet;
}
我们还是利用上面的两个集合接着求其交集：

var intersect = fruits1.intersect( fruits2 );
intersect.show();                               // ["Banana", "Pear"]
下一个定义的操作是 subset ；

subset：判断集合是否是另一集合的子集

该方法首先要确定 该集合的长度是否小于待比较的集合。如果该集合比待比较集合还要大，那么肯定不是待比较集合的一个子集。只要当，待比较集合比较大时，才去判断集合类的成员是否都属于待比较集合，如果有一个不是，直接返回 false ， 只有当所有元素都属于待比较集合的时候，我们才能说该集合是待比较集合的一个子集，该方法才会返回 true , 为了方便查看，我加入了console打印；

//子集判断

function subset (set) {
    if( this.size() > set.size() ){
        console.log('not a subset');
        return false;
    }else{
        for ( var i = 0 ; i < this.dataStore.length ; i++ ){
            if( !set.contains(this.dataStore[i])){
                console.log('not a subset');
                return false;
            }
        }
    }
    console.log(' a subset');
    return true;
}
我们看到上面用到了 size 方法，它的定义如下：

//返回集合长度

function size () {
    return this.dataStore.length;
}
我们保留上面的 fruits1 和 fruits2 ， 新建一个 fruits3 来演示 subset 方法

var fruits3  = new MySet();
fruits3.add('Apple');
fruits3.add('Banana');
fruits3.add('Pear');
fruits3.add('Grape');
fruits3.add('Orange');

//子集判断

fruits1.subset( fruits2 );      // not a subset
fruits2.subset( fruits2 );      // a subset
fruits1.subset( fruits3 );      // a subset
看起来一切都很顺利，我们只剩最后一个 difference 方法，该方法返回一个新集合，该集合是由属于第一个集合而不属于第二个集合的成员组成的。

difference：补集

有了交集的思路，补集的实现就显得很自然了。

//补集

function difference (set) {
    var tempSet = new  MySet();
    for( var i = 0 ; i < this.dataStore.length ; i ++ ){
        if( !set.contains(this.dataStore[i])){
            tempSet.dataStore.push( this.dataStore[i] );
        }
    }
    return tempSet;
}
我们测试一下：

fruits1.difference(fruits2).show();     // ['Apple']
fruits1.difference(fruits3).show();     // []
fruits2.difference(fruits1).show();     // ["Grape", "Orange"]
ok，到现在，我们完成了一个完整的 set 集合，是不是很棒！
