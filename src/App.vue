<template>
  <div>
    <Suspense>
      <template #default>
        <AsyncShow />
      </template>
      <template #fallback>
        <h1>Loading...</h1>
      </template>
    </Suspense>
  </div>
  <img alt="Vue logo"
       src="./assets/logo.png" />
  <div>
    <h2>欢迎光临红浪漫洗浴中心</h2>
    <div>请选择一位美女为你服务</div>
    <div v-if="loading">Loading.....</div>
    <img v-if="loaded"
         :src="result.message" />
  </div>
  <div>
    <button @click="selectGirlFun(index)"
            v-for="(item, index) in girls"
            :key="index">
      {{ index }} : {{ item }}
    </button>
  </div>
  <div>你选择了【{{ selectGirl }}】为你服务</div>
  <div><button @click="overAction">点餐完毕</button></div>
  <div>{{ overText }}</div>
  <div><button @click="getNowTime">现在时间</button>:{{nowTime}}</div>
  <modal></modal>

</template>

<script lang="ts">
import {
  ref,
  defineComponent,
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  reactive,
  toRefs,
  // onRenderTracked,
  // onRenderTriggered,
  watch,
} from "vue";
import { nowTime, getNowTime } from "./hooks/useNowTime";
import useUrlAxios from "./hooks/useAxios";
import modal from "./components/Modal.vue";
import AsyncShow from "./components/AsyncShow.vue";

interface DataProps {
  girls: string[];
  selectGirl: string;
  selectGirlFun: (index: number) => void;
}

export default defineComponent({
  name: "App",
  components: { modal, AsyncShow },
  setup() {
    console.log("1-开始创建组件-----setup()");
    // const girls = ref(["1号", "2号", "3号"]);
    // const selectGirl = ref("");
    // const selectGirlFun = (index: number) => {
    //   selectGirl.value = girls.value[index];
    // };
    onBeforeMount(() => {
      console.log("2-组件挂载到页面之前执行-----onBeforeMount()");
    });

    onMounted(() => {
      console.log("3-组件挂载到页面之后执行-----onMounted()");
    });
    onBeforeUpdate(() => {
      console.log("4-组件更新之前-----onBeforeUpdate()");
    });
    onUpdated(() => {
      console.log("5-组件更新之后-----onUpdated()");
    });
    // onRenderTracked((event) => {
    //   console.log("状态跟踪", event);
    // });
    // onRenderTriggered((event) => {
    //   console.log("状态跟踪", event);
    // });
    const data: DataProps = reactive({
      girls: ["1号", "2号", "3号"],
      selectGirl: "",
      selectGirlFun: (index: number) => {
        data.selectGirl = data.girls[index];
      },
    });
    const overText = ref("红浪漫");
    const overAction = () => {
      overText.value = overText.value + "点餐完成 | ";
      // document.title = overText.value;
    };
    const reactData = toRefs(data);
    watch([overText, reactData.selectGirl], (newValue, oldValue) => {
      console.log("watch", newValue);
    });

    const { result, loading, loaded } = useUrlAxios(
      "https://dog.ceo/api/breeds/image/random"
    );

    return {
      ...reactData,
      overText,
      overAction,
      nowTime,
      getNowTime,
      result,
      loading,
      loaded,
    };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
