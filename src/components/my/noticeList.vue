<template>
	<div>
		


		<div id="list" v-show="isShow==1">
			
		<header class="_head">
    <img src="../../assets/common/images/back32.png" style="position:absolute;left:10px;top:10px;" onclick="javascript:history.back();">
    <span style="font-size: 20px;">当前公告</span>
    <img src="../../assets/common/images/add.png" style="position:absolute;right:10px;top:10px;" @click="publishNotice()">
    </header>
    <div class="_top_block"></div>

    
			<ul class="mui-table-view mui-table-view-chevron">

      <li class="mui-table-view-cell mui-media" v-for="item in items" @click="goNoticeDetail(item.noticeid)">
          <a class="mui-navigate-right" style="text-decoration: none">
            <img class="mui-media-object mui-pull-left" src="../../assets/common/images/cbd.jpg">
            <div class="mui-media-body">
              {{item.noticetitle}}
              <p class='mui-ellipsis'>{{item.noticeempname}}--<span style="font-size:12px;">{{item.noticetime}}</span></p>
            </div>
          </a>
        </li>		
				
			</ul>
		</div>


		<div id="detail" v-show="isShow==2">

      <header class="_head">
        <img src="../../assets/common/images/back32.png" style="position:absolute;left:10px;top:10px;" @click="changeShow()">
        <span style="font-size: 20px;">{{noticeEntity.noticetitle}}</span>
      </header>
      <div class="_top_block"></div>
  
  
  
      <div id="ls" style="margin-top:8px;width:100%;height:auto;background: white;">

        
        <div class="_line"></div>
        
        <div style="width:100%;height:auto;margin-top:15px;padding-bottom:15px;">
          <p style="margin-left:10px;">
            &nbsp;&nbsp;
            {{noticeEntity.noticecontent}}
            
          </p>
        </div>
        
        <div class="_line"></div>
        <div style="width:100%;height:25px;line-height: 25px;color:black;text-align: right;font-size:14px;">     
          {{noticeEntity.noticeempname}}{{noticeEntity.noticetime}}
        </div>
        <div class="_line"></div>
        <img :src="noticeEntity.noticeaddress" /> 
      
      </div>
		</div>



		<div id="addNotice" v-show="isShow==3">

      <header class="_head">
      <img src="../../assets/common/images/back32.png" style="position:absolute;left:10px;top:10px;"  @click="isShow=1">
      <span style="font-size: 20px;">添加公告</span>
      <img src="../../assets/common/images/save.png" style="position:absolute;right:10px;top:10px;width:25px;height:25px;" @click="addNotice2()">
      </header>
    

      <div class="_top_block"></div>
      

      <form id="frm1">

      <div id="_content" style="width:100%;height:auto;background: white;">

      <div class="_line"  style="height:8px;"></div>
          
      <div style="width:100%;height:45px;line-height: 45px;">
        <span style="display: block;position: absolute;left:10px;">标题：</span><span style="position:absolute;right: 10px;"><input type="text"  style="border:0px;width:250px;text-align: right;" placeholder="请输入标题" name="title" id="title"/></span>
      </div>
      
      <div class="_line"></div>
      <div style="width:100%;height:150px;line-height: 45px;margin-top:2px;">
        <textarea style="position:absolute;width:100%;height:150px;border:0px;" placeholder="请填写公告内容" name="content" id="content"></textarea>
        <input type="hidden" name="token" :value="token"/>
      </div>
      
      <div class="_line"></div>
      <div style="width:100%;height:45px;line-height: 45px;background:white;">
      <input type="file" name="pic" style="border:0px;margin-top:10px;margin-left:10px;" id="pic"/>

      </div>


        
      </div>

      </form>




		

		</div>


	</div>


</template>

<script type="text/javascript">
	//向后台接口发送数据，并获取值
	import {API} from '../../static/api.js'
    import axios from 'axios'
	  export default({
        data(){
            return {
                items:[],
                isShow:1,
                noticeEntity:{},
                token:'',

            }
        },
        created(){
          this.token=localStorage.getItem('token')
            this.$nextTick(function(){
            	//表示页面渲染完成之后
            	//this.initDom();
            	this.noticeListByEntId();
            })
		    },
        methods:{
        	  addNotice2:function(){
                  //在这里验证是否输入，如果没有输入，则提示后直接返回
                  //console.log("aaa="+document.getElementById("title").value);
                  if(document.getElementById("title").value==""){
                      mui.alert('请填写公告标题', '智能工单', function() {
                          
                      });
                      return ;
                  }

                  if(document.getElementById("content").value==""){
                      mui.alert('请填写公告内容', '智能工单', function() {
                          
                      });
                      return ;
                  }

                  var _this = this;
                  console.log("sendUnameByForm..");
                    //将表单对象封装成一个js对象
                  var formData=new FormData(document.getElementById("frm1"));
                    //设置内容类型为：multipart/form-data
                  var config={
                          headers:{'Content-Type':'multipart/form-data'}
                      };
                      //提交文件对象只能是post方法
                        axios.post(API.addNotice,
                          formData,
                          config
                        )
                        .then(function (response) {
                            //alert(123);
                            _this.isShow=1;
                            _this.noticeListByEntId();
                            //字段清空
                            document.getElementById("title").value="";
                            document.getElementById("content").value="";
                            document.getElementById("pic").value="";
                            obj.outerHTML=obj.outerHTML; 

                        })
                        .catch(function (error) {
                          console.log(error);
                        });
                },


              	publishNotice:function(){
              		this.isShow=3;
              	},
              	changeShow:function(){
              		this.isShow = 1;
              	},

        	       goNoticeDetail:function(nid){
        		//this.$router.push('noticeDetail')
        		//发送http请求，获取对应的noticeEntity对象，然后将entity对象的值绑定到详情里面

        		        console.log("nid==="+nid);
        		        var _this = this;
                    axios.get(API.noticeDetailByNoticeId,{
        					    params: {
              					token:_this.token,
              					noticeid:nid
            				  }
                    }).then(function (response) {
                      	_this.isShow= 2;
                      	console.log("123");
                	       _this.noticeEntity = response.data.data;
                    }).catch(function (error) {
                        console.log(error);
                });

        	},
            
           noticeListByEntId:function(){
           		var _this = this;
              axios.get(API.noticeListByEntId,{
					     params: {
      					 token:_this.token
    				    }
                }).then(function (response) {
                	//_this.isShow =1;
                	console.log("aaa="+response.data.data[0]);
                	_this.items = response.data.data;
                })
                .catch(function (error) {
                  console.log(error);
                });
              }


          }
    })


</script>

<style scoped>
				 .title {
				margin: 20px 15px 10px;
				color: #6d6d72;
				font-size: 15px;
			}

._head{position: fixed;top:0px;left:0px;width:100%;height:50px;background: white;text-align: center;line-height: 50px;}

._line{width:100%;height:2px;background: #f2f2f2;}
._top_block{width:100%;height:50px;}
</style>