<template>
    <div id='root'>
        <div class='header'>
            <div class='button-app' v-if='page!=0' @click="page=0">
                <img src='./assets/back.svg'/>
            </div>
            <div class='title home' v-if='page==0'>Fake Yiban v0.2.3 (Alpha)</div>
            <div class='title' v-if='page==-1'>关于</div>
            <div class='title' v-else-if='page==1'>{{studentName}}的请销假</div>
            <div class='title' v-else-if='page==2'>请销假</div>
            <div class='button-app' v-if='page==0' @click='page=-1'>
                <img src='./assets/about.svg'/>
            </div>
            <div class='button-app' v-if='page>0'>
                <img src='./assets/close.svg'/>
            </div>
            <div class='button-app' v-if='page>0'>
                <img src='./assets/menu.svg'/>
            </div>
        </div>
        <div class='content' v-if='page==0'>
            <div class="edit-title-detail">
                <div class="edit-title-detail-dot"></div>申请人
            </div>
            <div class="edit-detail-content">
                <div class='row no-delete'>
                    <div class='label'>姓名</div>
                    <div class='label'>学号</div>
                </div>
                <div class='row no-delete'>
                    <input type='text' v-model='studentName'>
                    <input type='text' v-model='studentID'>
                </div>
            </div>
            <div class="edit-title-detail">
                <div class="edit-title-detail-dot"></div>申请内容
            </div>
            <div class="edit-detail-content">
                <div class='row no-delete'>
                    <div class='label'>项目</div>
                    <div class='label'>内容</div>
                </div>
                <div v-for='item, num in items' :key='item'>
                    <div class='row' v-if='deletingItem==num' @blur="deletingItem=-1">
                        <button class='confirm' @click="items.splice(num, 1);deletingItem=-1">删除</button>
                        <button class='cancel' @click="deletingItem=-1">取消</button>
                    </div>
                    <div class='row' v-else>
                        <input type='text' v-model='item.name'>
                        <input type='text' v-model='item.value'>
                        <button class='delete' @click="deletingItem=num">
                            <img src='./assets/delete.svg'/>
                        </button>
                    </div>
                </div>
                <button class='add' @click="items.push({name: '', value: ''})">
                    <img src='./assets/add.svg'/>
                </button>
            </div>
            <div class="edit-title-detail">
                <div class="edit-title-detail-dot"></div>审批进度
            </div>
            <div class="edit-detail-content">
                <div class='row no-delete'>
                    <div class='label'>姓名</div>
                    <div class='label'>编号</div>
                    <div class='label'>时间</div>
                </div>
                <div v-for='approver, num in approvers' :key='approver'>
                    <div class='row' v-if='deletingApprover==num'>
                        <button class='confirm' @click="approvers.splice(num, 1);deletingApprover=-1">删除</button>
                        <button class='cancel' @click="deletingApprover = -1">取消</button>
                    </div>
                    <div class='row' v-else>
                        <input type='text' v-model='approver.name'>
                        <input type='text' v-model='approver.id'>
                        <input type='text' v-model='approver.time'>
                        <button class='delete' @click="deletingApprover = num">
                            <img src='./assets/delete.svg'/>
                        </button>
                    </div>
                </div>
                <button class='add' @click="approvers.push({name: '', id: '', time: ''})">
                    <img src='./assets/add.svg'/>
                </button>
            </div>
            <div class="edit-title-detail">
                <div class="edit-title-detail-dot"></div>已抄送 
            </div>
            <div class="edit-detail-content">
                <div class='row no-delete'>
                    <div class='label'>姓名</div>
                    <div class='label'>编号</div>
                </div>
                <div v-for='cc, num in ccs' :key='cc'>
                    <div class='row' v-if='deletingCC==num'>
                        <button class='confirm' @click="ccs.splice(num, 1);deletingCC=-1">删除</button>
                        <button class='cancel' @click="deletingCC=-1">取消</button>
                    </div>
                    <div class='row' v-else>
                        <input type='text' v-model='cc.name'>
                        <input type='text' v-model='cc.id'>
                        <button class='delete' @click="deletingCC=num">
                            <img src='./assets/delete.svg'/>
                        </button>
                    </div>
                </div>
                <button class='add' @click="ccs.push({name: '', id: ''})">
                    <img src='./assets/add.svg'/>
                </button>
            </div>
        </div>
        <div class='final' v-if='page==0'>
            <button @click='go'>表单详情</button>
            <div class='separator'></div>
            <button @click='page=2'>离返校码</button>
        </div>
        <fake v-if='page == 1' :student-name='studentName' :studentID="studentID"
            :items='items' :approvers="approvers" :ccs="ccs" @exit='page=0'></fake>
        <QRCode v-if='page == 2' @exit='page=0'></QRCode>
        <div class='about' v-if='page == -1'>
        </div>
    </div>
</template>

<script>
import Fake from './components/fake/Fake.vue'
import QRCode from './components/fake/QRCode.vue'
import dayjs from 'dayjs'

function random(len) {
  var str = "";
  for (var idx = 0; idx < len; idx++) {
    str = str + Math.floor(Math.random() * 10);
  }
  return str;
}

function randomPhone() {
  var prefixArray = new Array("130", "131", "132", "133", "135", "137", "138", "170", "187", "189");
  var i = parseInt(10 * Math.random());
  var prefix = prefixArray[i] + random(8);
  return prefix
}

export default {
    name: 'App',
    components: {
        Fake,
        QRCode
    },
    data(){
        return {
            page: 0,
            studentName: '田所浩二',
            studentID: dayjs().add(-1, 'year').format('YYYY').replace(/[^0-9]/ig, "") + random(8),
            items: [
                {
                    name: "审批编号",
                    value: dayjs().format('YYYY-MM-DD 07:55').replace(/[^0-9]/ig, "") + random(8)
                },
                {
                    name: "是否离校",
                    value: "是"
                },
                {
                    name: "开始时间",
                    value: dayjs().format('YYYY-MM-DD 08:00')
                },
                {
                    name: "结束时间",
                    value: dayjs().format('YYYY-MM-DD 20:00')
                },
                {
                    name: "时长(天)",
                    value: "0.5"
                },
                {
                    name: "请假类型",
                    value: "事假"
                },
                {
                    name: "本人联系电话",
                    value: randomPhone()
                },
                {
                    name: "紧急联系人",
                    value: ""
                },
                {
                    name: "紧急联系电话",
                    value: randomPhone()
                },
                {
                    name: "请假原因",
                    value: ""
                }
            ],
            approvers: [{
              name: "",
              id: '10'+random(4),
              time: dayjs().format('YYYY-MM-DD 07:55')
            }],
            ccs: [],
            deletingItem: -1,
            deletingApprover: -1,
            deletingCC: -1
        }
    },
    methods: {
        go(){
            window.localStorage.setItem('last-data', JSON.stringify(this.$data));
            this.page = 1;
        },
        setButtonCancel(el){
            if(el){
                this.buttonCancel = el;
            }
        }
    },
    mounted(){
        let lastDataJson = window.localStorage.getItem('last-data');
        if(lastDataJson){
            let data = JSON.parse(lastDataJson);
            this.studentName = data.studentName;
            this.studentID = data.studentID;
            this.items = data.items;
            this.approvers = data.approvers;
            this.ccs = data.ccs;
        }
    }
}
</script>

<style src='./app.css'></style>
