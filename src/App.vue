<template>
    <div id="app">
        <h1 v-html="title"></h1>
        <div class="addPerson">
            <input class="playerAddInput" placeholder="少年，快与牌姬签订契约吧" v-model="newItem" v-on:keyup.enter="addNew"/>
            <button class="playerAddBtn" @click="addNew" >添加经验宝宝</button>
        </div>
        
        <div class="selectBar">
            <span class="rankLooking selected" @click="rankLooking">查看排名</span>
            <span class="resultInput" @click="resultInput">录入战绩</span>
        </div>
        
        
        <table class="scoreTable" v-show="tableShow">
            <tr>
                <th>参赛者</th>
                <th class="narrow">积分</th>
                <th class="narrow">对战局数</th>
                <th class="narrow">top数</th>
                <th class="long">诈和罚分</th>
                <th class="narrow">退赛</th>
            </tr>
            <tr v-for="(item,index) in items" :key="index">
                <td>{{item.name}}</td>
                <td class="narrow">{{item.score}}</td>
                <td class="narrow">{{item.playTimes}}</td>
                <td class="narrow">{{item.topNum}}</td>
                <td class="long"><input class="changingScore" type="number" v-model="changingScore[index]"><button class="wrongBtn" @click="wrongRon(item,index)">罚分</button></td>
                <td class="narrow"><button class="deleteBtn" @click="deletePlayer(item,index)">删除</button></td>
            </tr>
        </table>
        
        <table class="resultTable" v-show="resultShow">
            <tr>
                <th></th>
                <th>参赛者</th>
                <th>点数</th>
            </tr> 
            <tr>
                <td>一位</td>
                <td>
                    <select v-model="firPlayer">
                        <option v-for="(user,index) in items" :key="index" :value="user.name">{{user.name}}</option>
                    </select>
                </td>
                <td><input class="score" type="number" v-model="score[0]"/></td>
            </tr>   
            <tr>
                <td>二位</td>
                <td>
                    <select v-model="secPlayer">
                        <option v-for="(user,index) in items" :key="index" :value="user.name">{{user.name}}</option>
                    </select>
                </td>
                <td><input class="score" type="number" v-model="score[1]"/></td>
            </tr>   
            <tr>
                <td>三位</td>
                <td>
                    <select v-model="thiPlayer">
                        <option v-for="(user,index) in items" :key="index" :value="user.name">{{user.name}}</option>
                    </select>
                </td>
                <td><input class="score" type="number" v-model="score[2]"/></td>
            </tr>   
            <tr>
                <td>四位</td>
                <td>
                    <select v-model="forPlayer">
                        <option v-for="(user,index) in items" :key="index" :value="user.name">{{user.name}}</option>
                    </select>
                </td>
                <td><input class="score" type="number" v-model="score[3]"/></td>
            </tr>
            <tr>
                <td>
                    <div class="total">
                        总计：{{totalScore}}
                    </div>
                </td>
                <td colspan="2"><button class="submit" @click="submit">确认提交</button></td>
            </tr>

        </table>
        
    </div>
</template>

<script>
    export default {
        data: function() {
            return {
                title: '五月日麻积分赛计分表',
                items: [],
                changingScore: [],
                score: [30000, 30000, 30000, 30000],
                participant: [],
                newItem: '',
                tableShow: true
            }
        },
        computed: {
            totalScore: function() {
                return (Number(this.score[0]) + Number(this.score[1]) + Number(this.score[2]) + Number(this.score[3]))
            }
        },
        methods: {
            addNew: function() {
                if (this.newItem != "") {
                    this.items.push({
                        name: this.newItem,
                        score: 0,
                        playTimes: 0,
                        topNum: 0
                    })
                    this.changingScore.push(0)
                }
                this.newItem = '',
                    this.items.sort(function(a, b) {
                        return b.score - a.score
                    })
            },
            wrongRon: function(item, index) {
                item.score += Number(this.changingScore[index])
                this.items.sort(function(a, b) {
                    return b.score - a.score
                })
            },
            deletePlayer: function(item, index) {
                this.items.splice(index, 1)
                this.changingScore.splice(index, 1)
            },
            rankLooking: function() {
                if ($(".resultInput").hasClass("selected")) {
                    $(".resultInput").removeClass("selected")
                    $(".rankLooking").addClass("selected")
                    this.tableShow = true
                    this.resultShow = false
                }
            },
            resultInput: function() {
                if ($(".rankLooking").hasClass("selected")) {
                    $(".rankLooking").removeClass("selected")
                    $(".resultInput").addClass("selected")
                    this.tableShow = false
                    this.resultShow = true
                }
            },
            submit: function() {
                if (Number(this.totalScore) !== 120000) {
                    alert("分数错误")
                } else if (this.firPlayer === undefined || this.secPlayer === undefined || this.thiPlayer === undefined || this.forPlayer === undefined) {
                    alert("请添加四位参赛选手")
                } else if (this.firPlayer == this.secPlayer || this.firPlayer == this.thiPlayer || this.firPlayer == this.forPlayer ||
                    this.secPlayer == this.thiPlayer || this.secPlayer == this.forPlayer || this.thiPlayer == this.forPlayer) {
                    alert("请正确选择选手")
                } else if (this.score[0] < this.score[1] || this.score[1] < this.score[2] || this.score[2] < this.score[3]) {
                    alert("请输入正确的顺位")
                } else {
                    for (var player = 0; player < this.items.length; player++) {
                        if (this.items[player].name == this.firPlayer) {
                            this.items[player].score += (this.score[0] - 30000) / 1000 + 15
                            this.items[player].topNum += 1
                            this.items[player].playTimes += 1
                        }
                        if (this.items[player].name == this.secPlayer) {
                            this.items[player].score += (this.score[1] - 30000) / 1000 + 5
                            this.items[player].playTimes += 1
                        }
                        if (this.items[player].name == this.thiPlayer) {
                            this.items[player].score += (this.score[2] - 30000) / 1000 - 5
                            this.items[player].playTimes += 1
                        }
                        if (this.items[player].name == this.forPlayer) {
                            this.items[player].score += (this.score[3] - 30000) / 1000 - 15
                            this.items[player].playTimes += 1
                        }
                    }
                    this.items.sort(function(a, b) {
                        return b.score - a.score
                    })
                    this.rankLooking()
                    this.firPlayer = this.secPlayer = this.thiPlayer = this.forPlayer = ''
                    this.score = [30000, 30000, 30000, 30000]
                }
            }
        }
    }

</script>

<style>
    * {
        margin: 0;
        padding: 0;
        font-size: 16px;
    }

    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }

    h1 {
        margin-top: 100px;
        font-size: 50px;
    }

    .playerAddInput {
        display: inline-block;
        height: 40px;
        width: 220px;
        background-color: white;
        border-radius: 5px;
        border: 2px solid #66ccff;
    }

    .playerAddInput:focus {
        opacity: 0.7;
    }

    .playerAddBtn {
        padding: 0 15px;
        height: 45px;
        background-color: #66ccff;
        border: none;
        border-radius: 5px;
        color: white;
        font-weight: bold;
    }

    .playerAddBtn:hover {
        opacity: 0.7;
    }

    .addPerson {
        margin-top: 60px;
    }

    table {
        margin: 50px auto;
        table-layout: fixed;
        empty-cells: show;
        border-collapse: collapse;
        border-radius: 3px;
    }

    table th {
        color: #000;
        background-color: #66ccff;
        color: white;
        font-size: 20px;
        font-weight: bold;
        opacity: 0.8;
    }

    table th,
    td {
        width: 150px;
        height: 30px;
        font-size: 0.95em;
        text-align: center;
        padding: 4px;
        border: 1px solid #dddddd;
        border-collapse: collapse
    }

    table tr:nth-child(odd) {
        background-color: #dbf2fe;
    }

    table tr:nth-child(even) {
        background-color: #fdfdfd;
    }

    .selectBar {
        min-width: 720px;
        width: 60%;
        height: 50px;
        margin: 50px auto;
        background-color: #eee;
        border-bottom-left-radius: 10px;
        border-bottom: 1px solid #aaa;
        border-left: 1px solid #aaa;
    }

    .selectBar span {
        height: 46px;
        width: 100px;
        float: left;
        background-color: #ccc;
        border-top: 5px solid #ccc;
        font-size: 16px;
        font-weight: bold;
        line-height: 50px;
    }

    .selectBar span:hover {
        cursor: pointer;
    }

    .rankLooking {
        margin-left: 30%;
    }

    .selected {
        float: left;
        background-color: #FFF !important;
        border-top: 5px solid #99ccff !important;
        border-left: 1px solid #C0C0C0;
        border-right: 1px solid #C0C0C0;
        color: #66ccff;
    }

    .submit,
    .wrongBtn,
    .deleteBtn {
        padding: 0 15px;
        height: 30px;
        background-color: #66ccff;
        border: none;
        border-radius: 3px;
        color: white;
        font-weight: bolder;
        opacity: 0.8;
    }

    .submit:hover,
    .wrongBtn:hover {
        opacity: 0.6;
    }

    select,
    .score,
    .changingScore {
        margin: 10px;
        width: 130px;
        height: 24px;
        border-radius: 3px;
        border: 1px solid #aaa;
        background-color: inherit;
        font-size: 16px;
        text-align: center;
    }

    button:hover {
        cursor: pointer;
    }

    .narrow {
        width: 80px;
    }

    .long {
        width: 250px;
    }

</style>
