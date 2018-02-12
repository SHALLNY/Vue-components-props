# Vue-components-props

差点又被vue项目中的组件传值搞死，还好用了watch监听props的方法解决掉了

1.需求诉求是：子组件1向子组件2传递数据，中间经过父组件起到公用组件的作用，子组件2作为接收数据时

            props:['msg']


2.此时对于该数据的显示很简单即可表示为

            {{msg}}

这个表示方法没问题的，但是对于想处理msg数据的情况下，是比较棘手的
刚开始使用了这几种方法


            return(){
                data{
                    this.msg = null,
                    //data1

                    this.props = =null,
                    //data2

                    this.props.msg =null,
                    //data3
                }
            }


显然这几种都是错的

3.正确的思路是需要用到watch方法进行检测数据值


    this.$watch('Hidemsg', function(){
        console.log(this.Hidemsg+'watch');
    });


嗯 这样的话，数据就可以解决了，处理的方法都需要写在watch里面

4.不过watch检测数据的这个方法有一个局限的地方
这个值的传递只针对改变的值有效执行
比如第一次传值数值1，第二次还传值1，该方法不会执行第二次

对于这个重复值传递不会再次执行的问题，也碰到了
我只是想传递的数据起到一种flag性质，所以会出现重复的值的情况，然后代码只执行一次，为了解决每次传值flag每次都执行，我用了一种笨方法，让它每次传值都都不一样

我只是想传递值大于等于3的就行，然后为了让不一样，又保证循环的不同的数据不要太大，就设置了5，意思就是3-5不停循环

子组件1 navbar.vue

            data(){
                return{
                    NavValue:3,
                }
            },

            //获取父组件传来的数据
            props:['Hidemsg'],


设置本地的数据NavValue为3，与传值进行比较，如果相同即执行命令，此时的数值都是自增1的，确保每次的传值都不同

            this.$watch('Hidemsg', function(){
                 if(this.NavValue>5){
                     this.NavValue = 3;
                 }

                 if(this.Hidemsg == this.NavValue){
                     sessionStorage.setItem('objStr', this.Hidemsg);
                     this.$emit('transferNav',this.Hidemsg);
                     this.heightlight = this.Hidemsg;
                     this.index = this.Hidemsg;
                     this.NavValue++;
                 }

             });


子组件2 selfcenter.vue

            HideValue:3,

子组件2接受到公用父组件的数据后，与自身的HideValue进行判断，如果相同即执行数据

            methods: {

                //个人信息界面，点击遮罩层后信息界面消失
                hideSelfcenter(){
                    if(this.HideValue>5){
                        this.HideValue=3;
                    }
                    this.index = this.HideValue;
                    this.$emit('transferHide',this.HideValue);
                    this.HideValue ++;

                }
            },