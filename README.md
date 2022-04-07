# VueStudy 基础知识

减少了DOM操作，只需要修改vue实例里面的数据。面向数据编程。

比如说实现一个简单功能，就是在页面上显示一个helloworld，然后2秒后显示hello。用Dom操作实现。

~~~html
 <div id="app"></div>
    <script>
        let app=document.querySelector('#app');
        app.innerHTML='hello,World';
        setTimeout(function(){
            app.innerHTML='hello'
        },2000);
    </script>
~~~

如果用Vue实现

~~~html
    <div id = 'app'>{{content}}</div>
    <script>
        var app = new Vue({
        el:"#app",
        data:{
            content:'hello world',
        }
    })
    setTimeout(function(){
        app.$data.content='hello'
    },2000)

    </script>
~~~



当一个 Vue 实例被创建时，它将 `data` 对象中的所有的 property 加入到 Vue 的**响应式系统**中。当这些 property 的值发生改变时，视图将会产生“响应”，即匹配更新为新的值。值得注意的是只有当实例被创建时就已经存在于 `data` 中的 property 才是**响应式**的。

## 一、Vue的基础语法
### 第一次提交
首先进入vue官网加载vue.js文件,开发版本，将其添加到项目内。<br/>

![image-20220404233412815](https://raw.githubusercontent.com/HYHL0909/VueStudy/main/202204051520180.png)

利用script在head里引入，这样子能够防止抖屏。<br/>新创建的vue对象也需要在script中。

~~~html
<head>
    <script src='./vue.js'></script>
</head>
<body>
    <div id='root'>
        {{msg}}
    </div>
    <script>
        new Vue({
            el:"#root",
            data:{
                msg:"hello world"
            }
        })
    </script>
</body>
~~~

或者通过cdn引入

~~~html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
~~~



### 第二次提交：挂载点，模板，实例之间的关系

el所指向的标签就是vue对象的挂载点，实例只会处理挂载点下(包括孙子元素，只要在挂载点里，都能用data的内容<br/>



~~~html
<head>
    <script src='./vue.js'></script>
</head>
<body>
    <div id='root'>
       <h1>
            {{msg}}  可以用
        </h1>
    </div>
    <div>
        
    {{msg}} 不可用
    </div>
    <script>
        new Vue({
            el:"#root",
            data:{
                msg:"hello world"
            }
        })
    </script>
</body>
~~~

模板：挂载点里的内容都叫模板；模板可以放在挂载点里，也可以放在vue对象的template里。<br/>
放在对象的模板里的优先级更高.

~~~html
<head>
    <script src='./vue.js'></script>
</head>
<body>
    <div id='root'>

    </div>
    <div>
        
    {{msg}} 不可用
    </div>
    <script>
        new Vue({
            el:"#root",
            <!--   根据template和data构造一个dom，然后挂载el上-->
            template:"  <h1>{{msg}}  </h1>"
            data:{
                msg:"hello world"
            }
        })
    </script>
</body>
~~~



### 第三次提交 数据，事件，和方法
1. 数据：

   - {{插值表达式}}  

   - v-text和v-html都可以显示，但是text会进行转义，而html不会进行转义，会当作html代码执行，会加粗。<br/>

     ~~~html
     <div v-text='content'>
         
     </div>
     ~~~

2. 给模板上的标签绑定事件，比如点击标签后内容发生改变<br/>
   v-on:click="函数"，函数定义在vue对象的methods属性里，修改内容的方法直接修改this.content<br/>

   ~~~html
   <head>
       <script src='./vue.js'></script>
   </head>
   <body>
       <div id='root'>
          
           <div v-on:click="handleClick">
               {{content}}
           </div>
            <!--v-on:@-->
           <div @click="handleClick">
               {{content}}
           </div>
   
       </div>
   
       <script>
           new Vue({
               el:"#root",
               data:{
                   content:"hello world"
               },
               methods:{
                   handleClick:function(){
                       this.content='world';
                   }
               }
           })
       </script>
   </body>
   ~~~
   
   

### 第四次提交：属性绑定和双向数据绑定

1. 属性绑定 v-bind

   Mustache 语法不能作用在 HTML attribute 上，遇到这种情况应该使用 [`v-bind` 指令]  缩写：绑定元素属性。

   ~~~html
   <head>
       <script src='./vue.js'></script>
   </head>
   <body>
       <div id='root'>
           <!--上面显示的是title字符串-->
           <div title="title">hello world</div>
           
            <!--上面显示的是title里的内容，它指的的是vue实例中的title-->
            <div v-bind:title="title">hello world</div>
           <div :title="title">hello world</div>
           
       </div>
   
       <script>
           new Vue({
               el:"#root",
               data:{
                   title:"hello world"
               }
           })
       </script>
   </body>
   ~~~

   

2. 双向属性绑定。

   单向是，data决定页面的显示。

   v-model 实现表单数据的双向绑定，可以理解为直接修改了对象里的data。

   ~~~html
   <head>
       <script src='./vue.js'></script>
   </head>
   <body>
       <div id='root'>
           <div :title="title">hello world</div>
           
           <input v-model="content"/>
           <div>
               {{content}}
           </div>
           
       </div>
   
       <script>
           new Vue({
               el:"#root",
               data:{
                   title:"hello world",
                   content:"this is content"
               }
           })
       </script>
   </body>
   ~~~

   

### 第五次提交：计算属性和侦听器

1. 计算属性

   一个属性通过其他两个及以上属性计算而成。

   有个好处是如果实例内的属性值均没有改变的话，那么这个属性只会到缓存中寻找不会重新计算。但是一旦，实例内的属性值发生了改变，则会重新计算，此时包括着非实例内的属性一起计算。监听实例内的属性。

2. 侦听watch，修改次数等，就是计数器。

   一般是一个变量

   ~~~html
   <body>  
       <div id="root">
           姓：<input v-model="firstName"/>
           名：<input v-model="lastName"/>
           <div>{{fullName}}</div>
           <div>
               {{count}}
           </div>
         
    
         </div>
       <script>
           new Vue({
               el:"#root",
               data:{
                   firstName:"",
                   lastName:'',
                   count:0
   
               },
               computed:{
                   fullName:function(){
                       return this.firstName+" "+this.lastName;
                   }
               },
               /*监听fullName变量*/
               watch:{
                   fullName:function(){
                       this.count++;
                   }
               }
    
           })
       </script>
   </body>
   ~~~

   计算属性的getter和setter
   
   ~~~html
     <div id="app">
           <div> {{fullName}}</div>
          
       </div>
       <script>
           var vm = new Vue({
               el:'#app',
               data:{
                   firstName:'Elvira',
                   lastName:"Huang",
   
               },
               computed:{
                   fullName:{
                       /*当你使用fullName的时候，就会被调用*/
                       get:function(){
                           return this.firstName+" "+this.lastName;
                       },
                       /*当你给fullName赋值的时候。就会调用。当我们在控制台修改全名，显示在页面上的名字也被一同修改了。*/
                       set:function(value){
                           var arr=value.split(' ');
                           this.firstName=arr[0];
                           this.lastName = arr[1];
                       }
                   }
               }
   
           })
       </script>
   ~~~
   
   



### 第六次提交：（条件渲染）v-if，v-show，与（列表渲染）v-for指令

1. v-if 为false的时候，会直接将该节点从dom树上删除。如果很频繁，使用这种方法是很不合理的，要一直创建dom树。

   v-else-if  v-else![image-20220406213911717](https://raw.githubusercontent.com/HYHL0909/images/main/202204062139975.png)

   v-show为false的时候，会给该节点添加display：none，这个节点依然没有删除。

   ~~~html
   <body>  
       <div id="root">
         <div v-if="seen">hello world!</div>
          <!--可有可无-->
           <div v-else>
               hello Vue!
           </div>
         <div v-show="seen">hello Vue</div>
         <button @click="switcher">toggle</button>
   
       </div> 
       <script>
           new Vue({
               el:"#root",
               data:{
                   seen:true,     
               },
               methods:{
                   switcher:function(){
                       this.seen = !this.seen;
                   }
               }
           })
       </script>
   </body>
   ~~~

2. v-for :循环显示

   对数组的操作，只有部分操作才能够有相应式的效果，pop  push reverse splice

    unshift shift sort

   ~~~html
   <body>  
       <div id="root">
         <ol>
             <li v-for="item of list" >{{item}}</li>
         </ol>
   
       </div> 
       <script>
           new Vue({
               el:"#root",
               data:{
                   list:['apple','banana','orange']
               },
   
           })
       </script>
   </body>
   ~~~

     ~~~html
     <!--循环打印对象内容-->
      <div v-for="item of list" >
       <div v-if="item.age>20">
           {{item.age}}
          </div>   </div>
     ~~~

   

### 第七次提交 样式绑定

将数据和样式联系在一起。让数据改变，样式也跟着改变。

#### class的对象和数组绑定



- class的对象绑定。我给它一个类名，不是真的给。只有在为true的情况下才会给。然后给类设置样式。只有你有这个类时，你才有样式。否则没有。
- class与数组绑定。

~~~html
  <style>
        .activated{
            color:red;
        }
    </style>
 <div class="app">
     <!-- 对象赋值 -->
        <div :class={activated:isActivated}
        @click="changeStyle">Elvira Huang</div>
      <!-- 数组赋值 -->
        <div :class="[content]"
        @click="changetoRed">Elvis Han</div>
    </div>
    <script>
        var vm = new Vue({
            el:'.app',
            data:{
                isActivated:false,
                content:""
            },
            methods:{
                changeStyle:function(){
                    this.isActivated=!this.isActivated;
                },
                changetoRed:function(){
                    this.content=this.content==='activated'?"":"activated";
                }
            }


        })
    </script>
~~~

#### style的对象绑定和数组绑定



~~~html
<div class="app">
        <div :style="[colorStyle,fontSize]"
        @click="changeStyle">Elvira Huang</div>
    </div>
    <script>
        var vm = new Vue({
            el:'.app',
            data:{
                colorStyle:{
                    color:'black',
                },
                fontSize:{
                    'font-size':'48px'
                }
                
         
            },
            methods:{
                changeStyle:function(){
                  this.colorStyle.color = this.colorStyle.color==="black"?"red":"black";
                },
            }


        })
    </script>
~~~



## 二、MVVM模型

首先看看MVP模型

![image-20220406111729906](https://raw.githubusercontent.com/HYHL0909/images/main/202204061117143.png)

View可以理解为dom元素，页面展示出来的。

它如果想要改变内容，或者事件啊啥的，在vue之前只能通过dom操作。

presenter可以理解为dom操作。

model可以理解为数据库啊，后台数据之类的。

现在再看看MVVM层。

![image-20220406112828562](https://raw.githubusercontent.com/HYHL0909/images/main/202204061128678.png)





## 三、 Vue中的组件

### 基础知识：

#### 1. is的使用

table里必须是tr  

select里必须是option

ol/ul下建议是li

因此如果我们想要用我们自己的组件，可以使用 is

~~~html
<div id="root">
    <table>
    <tr is='row'></tr>
    </table>
</div>
<script>
    Vue.component('row',{
        template:'<tr><td>this is a row</td></tr>'
    })
    
    var vm=new Vue({
        ell:'#root',
    })
</script>
~~~



#### 2.实例中data的形式



子组件和根组件一样都是一个个的Vue实例。

根实例下的data可以用对象的，但是其他Vue实例下的data就只能够使用函数的格式,返回是一个对象的值。

~~~html
<div id="root">
    <table>
    <tr is='row'></tr>
    </table>
</div>
<script>
    Vue.component('row',{
        data:function(){
            return {
                content:'this is a row'
            }
        }
        template:'<tr><td>{{content}}</td></tr>'
    })
    
    var vm=new Vue({
        ell:'#root',
    })
</script>
~~~

#### 3.子组件/dom中refs 的使用。

如果我们非要使用子组件的属性，之类的，我们可以使用ref。下面是一个例子。

total是父组件的属性，它是由子组件的属性num1和num2相加而成的，因此父组件要获取到子组件的属性。

~~~html
    <div id="root">
        num1:<counter @change="handleTotal"
        ref="num1"></counter>
        num2:<counter @change="handleTotal"
        ref="num2"></counter>
        total:<div>{{total}}</div>

    </div>
    <script>
        Vue.component('counter',{
            data:function(){
                return {
                    number:0,
                }
            },
            template:'<div @click="increase">{{number}}</div>',
            methods:{
                increase:function(){
                    this.number++;
                    this.$emit('change')
                }
            }

        })
        let vm = new Vue({
            el:'#root',
            data:{
                total:0,

            },
            methods:{
                handleTotal:function(){
                    this.total= this.$refs.num1.number+this.$refs.num2.number;
                }

            }
          

        })
    </script>
~~~

#### 4.父子组件间的传值

父组件通过属性的方式向子组件传递数据

props

父组件可以修改该属性，但是子组件只能用不要去修改。如果非要修改，你就自己拷贝一个副本存储要修改的属性，然后改自己的副本。

~~~html
<div id="root">
        <counter :count="5"></counter>
    </div>
 
    <script>
        let counter={
             props:['count'],
            data:function(){
                return{
                    number:this.count,
                }
            },
            template:'<div @click="inc">{{number}}</div>',
            methods:{
                inc:function(){
                    this.number++;
                }
            }
        }
        let vm=new Vue({
            el:'#root',
            components:{
                counter:counter,
            }
            
        })

    </script>
~~~

#### 5.组件参数校验与非Props特性

~~~javascript
Vue.component('child',{
    props:{
        content:{
            type:String,//我希望接受过来的content类型 是String
            required:false,//这个值不是必须传的，如果是true那么这个值是必传的。
            default:'  ',//这个值如果没有传递过来，那么就使用默认的值。
            
            /*对传递过来的值使用校验器。*/
            validator:function(value){
                return (value.length>5);
            }
        }
    }
})
~~~

pros特性：父组件传，子组件接（声明props属性），子组件可以用，属性不会显示在dom上。

非props特性，父组件传，子不接，子不能用，属性显示在dom上

####  6.给组件绑定原生事件

假设counter是我们自己定义的一个子组件，我们在父组件上使用了它，这样click就是一个自定义的事件，用来监听子组件发出$emit触发的事件。如果要让他称为原生的点击事件，则需要加上native

~~~html
<counter @click="handleTotal"ref="num1"></counter>

<counter @click.native="handleTotal"ref="num1"></counter>
~~~

#### 7 非父子组件之间的传值

![image-20220407141948251](https://raw.githubusercontent.com/HYHL0909/images/main/202204071419502.png)

利用总线机制。/发布订阅模式/观察者模式

你可以理解为广播，然后需要接受的去监听。

~~~html
  <div class="root">
        <child content="Elvira"></child>
        <child content="Huang"></child>
    </div>
    <script>
        // 在vue上挂了一个属性，那么每次利用vue创建的实例都会有这个bus属性，指向同一个vue实例
        Vue.prototype.bus=new Vue()

        Vue.component('child',{
            props:{
                content:String,
            },
            data:function(){
                return {
                    selfContent:this.content
                }
            },
            template:'<div @click="handleClick">{{selfContent}}</div>',
            methods:{
                handleClick:function(){
                 this.bus.$emit('change',this.selfContent)  
                }
            },

            //每个子组件去添加未免太过于麻烦，因此用自带的生命周期钩子。
            mounted:function(){
                //记得提前保存this
                let this_=this;
                this.bus.$on('change',function(msg){
                    //如过用this，这时候的this指代的是this.bus
                    this_.selfContent=msg;
                })
               
            }

        })

        let vm=new Vue({
            el:'.root',
        })
    </script>
~~~

#### 8 插槽--父组件向子组件传递任何东西

~~~html
<navigation-link url="/profile">
  <!-- 添加一个图标的组件 -->
  <font-awesome-icon name="user"></font-awesome-icon>
  Your Profile
</navigation-link>
~~~

然后在`</navigation-link>`组件的模板可能写着

~~~html
<div>
    <slot> default</slot>
</div>
~~~

此时slot就是 父组件给子组件里插入的所有。

~~~html
  <!-- 添加一个图标的组件 -->
  <font-awesome-icon name="user"></font-awesome-icon>
  Your Profile
~~~

如果 `<navigation-link>` 的 `template` 中**没有**包含一个 `<slot>` 元素，则该组件起始标签和结束标签之间的任何内容都会被抛弃。

如果template里有slot但是父级组件不提供任何插槽内容时，default内容将会被渲染。

例题：

如果我们只要插槽中的一部分内容，我们不想要那么多都变成slot可以吗

可以的，使用命名的方法。具名插槽。

~~~html
 <div id="root">
       <child>
           <div slot="header">header</div>
           <div slot="footer">footer</div>
       </child>

    </div>
    <script>            
        Vue.component('child',{
            template:`
            <div> 
            <slot name="header"></slot>
            <div>Content</div>  
            <slot name='footer'></slot> 
        </div>`
        })


        let vm=new Vue({
            el:'#root'
        })
    </script>
~~~

#### 9 作用域插槽

template开头和结尾

子组件向父组件的插槽传递数据。

#### 10 动态组件

可以通过component 和is 连用实现组件的切换，当在这些组件之间切换的时候，当切换到另一个组件时，当前的组件会被销毁，然后创建另一个组件。

你有时会想保持这些组件的状态，以避免反复重渲染导致的性能问题。

重新创建动态组件的行为通常是非常有用的，但是在这个案例中，我们更希望那些标签的组件实例能够被在它们第一次被创建的时候缓存下来。为了解决这个问题，我们可以用一个 `<keep-alive>` 元素将其动态组件包裹起来。

~~~html
 <div id="root">
        <keep-alive>
            <component :is="type"></component>
        </keep-alive>
        <button @click="handleClick">change</button>

    </div>
    <script>
        Vue.component('first-name',{
            template:'<div>Elvira</div>'

        })
        Vue.component('last-name',{
            template:'<div>Huang</div>'

        })
        let vm = new Vue({
           el:'#root', 
           data:{
               type:'first-name'
           },
           methods:{
               handleClick(){
                   this.type=(this.type==='first-name'?'last-name':'first-name')
               }
           }
        
        })
    </script>
~~~

注意：我不想要type 直接传输名字可以吗，我试了一下好像是不可以的。

因为v-bind 指令后，“”里的内容不再是字符串，而是js表达 式。



### Vue中动画特效

#### 1.vue的过渡动画样式 

首先明确，vue在插入、移除dom的样式时提供了应用过渡效果。

过渡效果的原理是为dom元素添加类，而类上的样式早就已经确定好了。

Vue 提供了 `transition` 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡

- 条件渲染 (使用 `v-if`)
- 条件展示 (使用 `v-show`)
- 动态组件
- 组件根节点

首先来看看原理。

![image-20220407192205054](https://raw.githubusercontent.com/HYHL0909/images/main/202204071922356.png)

1. fade-enter：如果没有命名就叫v-enter：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。则里面的内容就不见了。

2. `v-enter-active`：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。

3. `v-enter-to`：**2.1.8 版及以上**定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 `v-enter` 被移除)，在过渡/动画完成之后移除。

   ![image-20220407194322792](https://raw.githubusercontent.com/HYHL0909/images/main/202204071943987.png)

4. `v-leave`：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。

5. `v-leave-active`：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。

6. `v-leave-to`：**2.1.8 版及以上**定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 `v-leave` 被删除)，在过渡/动画完成之后移除。

~~~html
<style>
        /* 本来fade-enter和fade-enter-active共存，此时opacity：0,下一帧fade-enter被清除，那么opacity就会变成1（default）。此时enter-active还是存在的，它会监视opacity的状态，，它看见opacity ：0-->1,它会将这个状态的改变持续3s */
        .fade-enter,.fade-leave-to{
            opacity:0;
        }
        .fade-enter-active,.fade-leave-active{
            transition: opacity 2s;
        }

    </style>
~~~



~~~html
<div id="root">
        
        <transition name="fade">    
        <div v-if="seen">Hello Elvira</div>
        </transition>   

        <button @click="handleClick">transition</button>

    </div>
    <script>
        let vm=new Vue({
            el:"#root",
            data:{
                seen:true,
            },
            methods:{
                handleClick:function(){
                    this.seen=!this.seen;
                }
            }
        })
    </script>
~~~

#### 2 vue的admitate动画

引用库

~~~html
 <link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">
~~~

网址看动画效果：https://animate.style/

transition只能用自己命名的。

~~~html
        <transition name="fade" enter-active-class="animated headShake"
        leave-active-class="animated tada">    
        <div v-if="seen">Hello Elvira</div>
        </transition>   
        <button @click="handleClick">transition</button>
~~~

如果想要第一次出现就有效果，加个appear

~~~html
 <transition name="fade" appear
        appear-active-class="animated headShake"
        enter-active-class="animated headShake"
        leave-active-class="animated tada">    
        <div v-if="seen">Hello Elvira</div>
        </transition>   
~~~

#### 3 animated和transition共同zuoyong

但是，在一些场景中，你需要给同一个元素同时设置两种过渡动效，比如 `animation` 很快的被触发并完成了，而 `transition` 效果还没结束。在这种情况中，你就需要使用 `type` attribute 并设置 `animation` 或 `transition` 来明确声明你需要 Vue 监听的类型。



你也可以定制进入和移出的持续时间：

~~~html
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
~~~



#### 4 Vue中多元素或者组件的过渡。

首先是多元素

~~~html
  <style>
        .fade-enter,.fade-leave-to{
            opacity:0;
        }
        .fade-enter-active,.fade-leave-active{
            transition: opacity 1s;
        }

    </style>


<transition name="fade" mode="out-in">   
     <!-- 如果没有key，因为dom元素的复用，效果是不明显的。   -->
        <div v-if="seen" key="hello">Hello Elvira</div>
        <div v-else key="buy">Buy Elvira</div>
        </transition>   
~~~



多组件的过渡，使用动态组件进行过渡。

就是在代码里添加个样式，这样就能够在切换的时候有效果了。

~~~html
就是动态组件的代码，然后在外面包个<transition></transition>
然后在transition提供的那些样式里添加样式。
~~~

#### 5 列表过渡

给使用v-for的元素用transition-group包起来，然后还是写 v-enter v-leave-to  等等函数。

#### 6 动画封装

~~~html
  Vue.component('fade',{
            props:['seen'],
            template:`
            <transition>
                <slot v-if="seen"></slot>
                </transition>`
        })
~~~

然后把要应用动画的元素抱起来

~~~html
<fade :seen="seen">
            <div>Hello Elvira</div>
        </fade>
~~~



### 实战

#### 1. todolist功能开发

点击提交，将内容显示在`li`里面。

~~~html
<div id="root">
        <div>
            <input v-model="inputValue">
            <button @click="add">submit</button>
        </div>
        <ul>
            <li v-for="(item,key) of list" :key="index">{{item}}</li>
        </ul>
    </div> 
    <script>
        new Vue({
            el:"#root",
            data:{
                inputValue:"",
                list:[]
            },
            methods:{
                add:function(){
                    this.list.push(this.inputValue);
                    this.inputValue='';
                  
                }
            }
        })
    </script>
~~~



#### 2.todolist组件拆分

组件就是页面里的部分。组件拆分的原则就是要复用性高

组件的定义与组件之间的通信。

全局组件：哪里都能用。局部组件只能在部分。

![image-20220406194405718](https://raw.githubusercontent.com/HYHL0909/images/main/202204061944935.png)

~~~html
<div id="root">
        <div>
            <input v-model="inputValue">
            <button @click="add">submit</button>
        </div>
        <ul>
            <todo-item v-for='item of list'
            :content="item" ></todo-item>
        </ul>
    </div> 
    <script>
        /*全局组件，它们在注册之后可以用在任何新创建的 Vue 根实例 (new Vue) 的模板中,props用于接受来自todo-item里面的content，父组件Vue，通过属性传递的方式，向子组件传递值*/
        Vue.component('todo-item',{
            props:['content'],
            template: '<li>{{content}}</li>' 
        })     
        new Vue({
            el:"#root",
            data:{
                inputValue:"",
                list:[]
            },
            methods:{
                add:function(){
                    this.list.push(this.inputValue);
                    this.inputValue='';
                  
                }
            }
        })
    </script>
~~~

~~~html
<div id="root">
        <div>
            <input v-model="inputValue">
            <button @click="add">submit</button>
        </div>
        <ul>
            <todo-item v-for='item of list'
                       :content='item'></todo-item>
        </ul>
    </div> 
    <script>    
        /*你可以通过一个普通的 JavaScript 对象来定义组件：*/
        var TodoItem = {
            props:['content'],
            template:'<li>{{content}}</li>'
        }
        new Vue({
            el:"#root",
            /*然后在 components 选项中定义你想要使用的组件，如果没有定义则使用不了，因为局部注册的组件在其子组件中不可用*/
            components:{ 'todo-item': TodoItem
        },      
            data:{
                inputValue:"",
                list:[]
            },
            methods:{
                add:function(){
                    this.list.push(this.inputValue);
                    this.inputValue='';               
                }
            }
        })
    </script>
~~~

#### 3.组件和实例的关系

组件本身也是一个vue实例。

Vue实例的模板是挂载在实例下的dom里的所有内容。

#### 4.实现todolist的删除功能。

==父子组件交互。==

添加，是在父组件中添加，然后将值传递给子组件，让子组件显示。

props用于接受来自todo-item里面的content，父组件Vue，通过属性传递的方式，向子组件传递值。所有的 prop 都使得其父子 prop 之间形成了一个**单向下行绑定**：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外变更父级组件的状态，从而导致你的应用的数据流向难以理解。

删除，比如点击删除，是在子组件里点击触发事件名，然后父组件监听到事件名被触发，然后父组件点击事件将父组件中渲染子组件的那条数据删除。

因此最重要的就是让父组件和子组件进行通信。

~~~html
<div id="root">
        <div>
            <input v-model="inputValue">
            <button @click="add">submit</button>
        </div>
        <ul>
            <todo-item v-for='(item,index) of list'
            :content="item"
            :key="index"
            :index="index" 
            @delete="handleDelete"></todo-item>
<!--父组件监听子组件是否有-->
        </ul>
    </div> 
    <script>
        Vue.component('todo-item',{
            props:['content','index'],
            template: '<li @click="handleClick">{{content}}</li>' ,
            methods:{
                handleClick:function(){
                    /*组件被点击会触发一个事件，事件叫delete，会传递this.index这个值*/
                    this.$emit('delete',this.index)

                }
            }
        })
        
        new Vue({
            el:"#root",
            data:{
                inputValue:"",
                list:[]
            },
            methods:{
                add:function(){
                    this.list.push(this.inputValue);
                    this.inputValue='';
                  
                },
                handleDelete:function(index){
                    this.list.splice(index,1);

                }
            }
        })
    </script>
~~~

## 三、 Vue-cli的简介与使用

Vue-cli自带webpack的

安装vue-cli

~~~npm
npm i -g vue-cli
vue init webpack my-project
cd my-project
npm run dev
~~~

主要是编写src的代码。

main.js

![image-20220404210259369](https://raw.githubusercontent.com/HYHL0909/VueStudy/main/202204051521638.png)

主要的Vue

~~~vue
<template>
</template>
<script>
   export default {
       /*导入子组件*/
       import TodoItem from './components/TodoItem.vue'
       components:{
           /*子组件*/
       },
       data () {
           return {}
       },
       method : {
           
       }
   } 
</script>
<style>
</style>
~~~

利用vue-cli实现todolist。

缕一缕

![image-20220404211407940](https://raw.githubusercontent.com/HYHL0909/VueStudy/main/202204051521477.png)

首先父组件的模板是：

~~~vue
<div>
  <div>
    <input >
    <button >submit</button>
  </div>
  <ul>
    <todo-item ></todo-item>
  </ul>
</div>
~~~

input的v-model inputValue，因为要双向绑定啊，相当于直接修改在父组件里的数据  inputValue

然后点击button时inputValue传递到list中。

然后显示list的东西，此时已经将li看成子组件。

父组件要使用子组件，要import，要定义components

然后父组件要把list上的数据传递给子组件，通过属性绑定。

：content=’item‘，然后子组件要利用props接受。如果要通过点击子组件删除数据。

要在子组件绑定click事件，然后事件触发$emit发送函数 名和参数给父组件

父组件在子组件上绑定了监听事件，一旦监听到子组件发送，就调用删除元素的函数。





style scoped

局部样式，局部有效，

这样很好，因为这样最大程度的保持了各个组件之间的解耦
