<template>
    <div>
      <h1>todoリスト</h1>
      <!-- todo入力フォーム -->
      <form @submit.prevent="addTodo()">
        <el-input placeholder="todo" v-model="todo"></el-input>
      </form>
      <el-row :gutter="12">
            <!-- todo表示エリア
            <el-col :span="12" v-for="( todo, index ) in todos" :key="index">
                <el-card class="box-card" shadow="hover" style="margin: 5px 0;">
                    <el-row :gutter="12">
                        <el-col :span="21">{{ todo }}</el-col>
                        <el-col :span="3">
                            <el-button @click="removeTodo(index)" type="success" icon="el-icon-check" circle></el-button>
                        </el-col>
                    </el-row>
                </el-card>
            </el-col>-->
            <TodoItem v-for="( todo, index ) in todos" :todo="todo" :key="index" @close="removeTodo(index)"/>
            <!-- issue表示エリア -->
            <el-col :span="12" v-for="( issue, index ) in issues" :key="issue.id">
                <el-card class="box-card" shadow="hover" style="margin: 5px 0;">
                    <el-row :gutter="12">
                        <el-col :span="21">{{ issue.title }}</el-col>
                        <el-col :span="3">
                            <el-button @click="closeIssue(index)" type="success" icon="el-icon-check" circle></el-button>
                        </el-col>
                    </el-row>
                </el-card>
            </el-col>
      </el-row>
    </div>
  </template>


<script>
import axios from 'axios';
import TodoItem from '@/components/TodoItem';

const client = axios.create({
  baseURL: 'https://api.github.com/repos/Lahanatou/Todo-projet',
  headers: { 
    'Accept': 'application/vnd.github.v3+json',
    'Content-Type':'application/json',
    'Authorization': `token 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`
  },
})

export default {
  name: 'TodosIssues',
  components: {
    TodoItem
  },
  data () {
    return {
      todo: '',
      todos: [],
      issues: [],
    }
  },
  methods: {
    // ここからtodoの管理
    addTodo(){
      this.todos.push(this.todo);
      this.todo= '';
    },
    removeTodo(index){
      this.todos.splice(index, 1);
    },
    // ここからissueの管理
    getIssues() {
    client.get('issues')
        .then((res) => {
          this.issues = res.data
      }).catch((error) => {
            console.log(error)
    })
    },
    closeIssue(index){
      const target = this.issues[index];
      client.patch(`/issues/${target.number}`,
          {
            state: "closed"
          },
        )
        .then(() => {
          this.issues.splice(index, 1);
      })
    },
  },
  created() {
    this.getIssues();
    }

}
</script>
