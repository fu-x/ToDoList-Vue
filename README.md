# 基于Vue的tudolist———最简单的代办事件列表

## Vue-toDolist分析：
1. 做到刷新页面不会丢失数据，因此我们的数据需要保存至本地localstorage里。

2. 每次加载页面都要先从本地获取一次数据保存至data数据中，通过vue数据绑定自动更新页面数据。

3. 修改数据后再把最新的数据保存至localstorage，这样保证本地存储的是最新的数据。

4. 存储的数据格式：var todolist = [{id: 0,title: '晚上看电影。', done:false}]。 其中title为事件内容，done表示事件是否完成。为了不操作dom元素，我们在删除和更改事件状态时，需要通过id找到被操作的事件在数据中的位置。

5. 通过v-for循环将数据显示到页面中。但是因为有已完成和未完成两种数据，我们需要声明两个函数对数组进行滤出。

6. 注意一：本地存储localStorage里面只能存储字符串格式，因此需要把对象转换为字符串JSON。stringfy(data)。

7. 注意二：获取本地存储数据，需要把里面的字符串转换为对象格式JSON.parse()我们才能使用里面的数据。

## 主要函数：
1. getData():从本地获取数据，保存到data中的todolist数组
```
    getData(){ //获取本地localStorage数据
        var data = localStorage.getItem('todolistVue');
        if(data !== null){
          this.todolist =  JSON.parse(data);
        }else{
          this.todolist = [];
        }
      },
```
2. saveData(data)：保存数据至本地中的todolistVue
```
    function saveData(data) { //保存数据至本地中的todolistVue
        localStorage.setItem('todolist',JSON.stringify(data));
    }
```
3. count():获取数据，并统计事件总数函数(这个函数需要在页面加载完成后最先调用一次，把最新的数据加载出来)
```
      count(){  //获取数据，并统计事件总数函数
        this.getData();
        this.num1 = 0; //初始化未完成事件
        this.num2 = 0; //初始化已完成时间
        this.todolist.forEach(ele => { //通过forEach循环遍历数据
          if(!ele.done){
            this.num1++;
          }else{
            this.num2++;
          }
        });
      },
```
4. add():添加事件函数（自动生成id）
```
      add(){ //添加事件函数（自动生成id）
        if(this.todolist != ""){ //如果本地数据不为空，保存当前最大的id号
          var id = this.todolist[0].id;
        }else{ //如果本地数据为空，初始化id为0；
          var id = 0; 
        }
        this.todolist.unshift({id:++id,title:this.newEvent,done:false}); //使用unshift永远将id最大的新数据放置数组首位
        this.saveData(this.todolist);
        this.newEvent = "";
        this.count();
      },
```
5. del(id):删除功能点击事件函数
```
      del(id){ //删除事件函数
        var index = this.todolist.findIndex(item => { //通过id查找该事件数据在todolist数组中的索引
          if(id == item.id)
            return true;
        })
        this.todolist.splice(index,1); //通过索引删除
        this.saveData(this.todolist);
        this.count();
      },
```
6. checkbox切换是否完成点击事件
```
    $('ol,ul').on('click','input',function () { //给checkbox（切换是否完成）绑定一个点击事件
        var data=getData(); //获取数据
        var index=$(this).siblings('a').attr('id'); //获取id
        data[index].done=$(this).prop('checked'); //让data数据中的checked值改为当前已修改的值
        saveData(data); //保存数据
        load(); //重新加载数据
    })
```
7. listDone()和listDont():对todolist数组进行过滤，分别过滤出已完成事件和未完成事件数据
```
    searchDone(list){ //过滤已完成的事件
        var doneList = [];
        this.todolist.forEach(ele =>{ //通过forEach循环将已完成的事件储存到doneList中
          if(ele.done){
            doneList.push(ele);
          }
        })
        return doneList;
      },
    searchDont(list){ //过滤出未完成的事件
        var dontList = [];
        this.todolist.forEach(ele =>{ //通过forEach循环将未完成的事件储存到dontList中
          if(!ele.done){
            dontList.push(ele);
          }
        })
        return dontList;
    }
```
