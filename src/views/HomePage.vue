<template>
  <ion-page>
    <ion-header :translucent="true" class="ion-no-border">
      <ion-toolbar color="dark">
        <div class="header-title">
          <ion-icon :icon="pencil"></ion-icon>
          <ion-label class="ion-padding-start">To Do List</ion-label>
        </div>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <div id="todo-container">
        <ion-item class="ion-no-padding ion-padding-top ion-padding-horizontal">
          <ion-item class="ion-no-padding todo-input-container" lines="none">
            <ion-label position="stacked" color="dark">Task</ion-label>
            <ion-input type="text" placeholder="Write a task" v-model="tarefa" v-on:keyup.enter="addItem()"></ion-input>
          </ion-item>
          <ion-button fill="solid" v-on:click="addItem()">
            <ion-icon :icon="add"></ion-icon>
          </ion-button>
        </ion-item>
        <ion-list class="ion-padding-horizontal" v-if="itens.length > 0">
          <ion-item v-for="(item, i) in itens" :key="i" v-on:click="setTask(item)">
            <ion-checkbox v-model="item.checked" slot="start"></ion-checkbox>
            <ion-label>{{ item?.text }}</ion-label>
          </ion-item>
        </ion-list>
  
        <ion-item class="todo-delete-container ion-padding-top" lines="none">
          <ion-button v-on:click="clearItens()" expand="block" fill="solid" size="default">
            <ion-icon :icon="trash"></ion-icon>
            <ion-label class="ion-padding-start">Delete tasks</ion-label>
          </ion-button>
        </ion-item>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { pencil, add, trash } from 'ionicons/icons';
import { IonContent, IonPage, alertController, IonButton,IonHeader, IonInput, IonToolbar, IonItem, IonLabel, IonCheckbox, IonList, IonIcon } from '@ionic/vue';
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'HomePage',
  components: {
    IonContent,
    IonPage,
    IonCheckbox,
    IonItem,
    IonLabel,
    IonButton,
    IonInput,
    IonList,
    IonHeader,
    IonToolbar,
    IonIcon
  },
  created(){
    const itens = localStorage.getItem("ToDo");
    if(itens) {
      this.itens = JSON.parse(itens);
    }

  },
  setup() {
    return {
      pencil, add, trash
    }
  },
  data() {
      const itens: any = [];
      return {
        itens: itens,
        tarefa: "",
      }
    },
  methods: {
    addItem(){

      if (!this.tarefa) {
        this.showMessage("Erro", "Adicione o nome da Tarefa!");
        return;
      }

      const toDo: any = localStorage.getItem("ToDo");
      const toDoItens = JSON.parse(toDo)
      const id = toDoItens ? toDoItens.length : 0;

      const item = { 
        id: id,
        text: this.tarefa,
        checked: false
      };
      this.itens.push(item);
      this.tarefa = "";

      localStorage.setItem("ToDo", JSON.stringify (
        toDo ? // Se tiver item no localStorage adiciona mais
        // senão adiciona só um
        [...toDoItens, item]:
        [item]
        )
      );

      this.showMessage("Sucesso", "Tarefa adicionada!");
    },
    clearItens(){
      this.showConfirm("Que deseja apagar todas as tarefas ?", ()=> {
        this.itens = [];
        this.tarefa = "";
        localStorage.removeItem("ToDo")
      });
    },
    setTask(task: any){
      const toDo: any = localStorage.getItem("ToDo");
      const toDoItens = JSON.parse(toDo);
      
      toDoItens.map(( item: any ) => {
        if (item.id == task.id) {
          item.checked = task.checked;
        }
      });

      localStorage.setItem("ToDo", JSON.stringify(toDoItens));

    },
    async showMessage(header?: string, message?:string){
      const alert = await alertController.create({
        header: header ?? 'Olá',
        message: message ?? 'Wellcome to ToDo App :)',
      });
      await alert.present();
    },
    async showConfirm(message: string, f: any){
      const confirm = await alertController.create({
        header: 'Tem certeza ?',
        message: message,
        buttons: [
          {
            text: 'Cancel',
            role: 'cancel',
            handler: () => {
              var t = 0;
            },
          },
          {
              text: 'OK',
              role: 'confirm',
              handler: f
          },
        ]
      })
      await confirm.present();
    }
  }
});

</script>

<style scoped lang="scss" src="./HomePage.scss">
  /* npm install -D sass-loader sass */
  /* lang="scss" */
</style>