<template>
  <div class="container is-fluid">
    <div class="columns">
      <div class="column is-two-thirds is-offset-one-fifth">
        <div class="content">
          <br>
          <h2>Animations</h2>
          <hr>
          <div class="field">
            <div class="control">
              <div class="select is-primary">
                <select v-model="alertAnimation">
                  <option value="fade">Fade</option>
                  <option value="slide">Slide</option>
                </select>
              </div>
            </div>
          </div>
          <button class="button is-primary" @click="show = !show">Animate</button>
          <br><br>
          <transition :name="alertAnimation">
            <div class="notification is-primary" v-if="show">
              This is some info.
            </div>
          </transition>
          <transition name="slide" type="animation">
            <div class="notification is-primary" v-if="show">
              This is some info.
            </div>
          </transition>
          <transition
            enter-active-class="animated bounce"
            leave-active-class="animated shake"
            appear>
            <div class="notification is-primary" v-if="show">
              This is some info.
            </div>
          </transition>
          <transition :name="alertAnimation" mode="out-in">
            <div class="notification is-warning" v-if="show" key="warning">
              This is a warning.
            </div>
            <div class="notification is-danger" v-else key="danger">
              This is critical.
            </div>
          </transition>
          <hr>
          <h3>JavaScript Animations (NO CSS)</h3>
          <button class="button is-link" @click="load = !load">Load/Remove Element</button>
          <br><br>
          <transition
            @before-enter="beforeEnter"
            @enter="enter"
            @after-enter="afterEnter"
            @after-cancelled="afterCancelled"

            @before-leave="beforeLeave"
            @leave="leave"
            @after-leave="afterLeave"
            @leave-cancelled="leaveCancelled"
            :css="false">
            <div style="width: 300px; height: 100px; background-color: lightgreen;" v-if="load"></div>
          </transition>
          <hr>
          <h3>Animating Dynamic Components</h3>
          <button
            class="button is-link"
            @click="selectedComponent == 'app-success-alert' ? selectedComponent = 'app-danger-alert' : selectedComponent = 'app-success-alert'">
            Toggle Components
          </button>
          <br><br>
          <transition name="fade" mode="out-in">
            <component :is="selectedComponent"></component>
          </transition>
          <hr>
          <button class="button is-primary" @click="addItem">Add Item</button>
          <h3>Animating Lists & Group Animation</h3>
          <ul>
            <transition-group name="slide">
                <li
                  v-for="(number, index) in numbers"
                  @click="removeItem(index)"
                  style="cursor: pointer;"
                  :key="number">
                    {{ number }}
                </li>
            </transition-group>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import DangerAlert from './DangerAlert.vue';
import SuccessAlert from './SuccessAlert.vue';

export default {
  data () {
    return {
      show: true,
      load: false,
      alertAnimation: 'fade',
      elementWidth: 100,
      selectedComponent: 'app-success-alert',
      numbers: [1, 2, 3, 4, 5]
    }
  },
  methods: {
    beforeEnter(el){
      console.log("before enter");
      this.elementWidth = 100;
      el.style.width = this.elementWidth + 'px';
    },
    enter(el, done){
      console.log('enter');
      let round = 1;
      const interval = setInterval(() => {
        el.style.width = (this.elementWidth + round * 10) + 'px';
        round++;
        if(round > 20){
          clearInterval(interval);
          done();
        }
      }, 20);
      // Needed to tell Vue when an animation has finished.
      // done();
    },
    afterEnter(el){
      console.log("after enter");
    },
    afterCancelled(el){
      console.log("after cancelled.");
    },
    beforeLeave(el){
      console.log("before leave");
      this.elementWidth = 300;
      el.style.width = this.elementWidth + 'px';
    },
    leave(el, done){
      console.log("leave");
      let round = 1;
      const interval = setInterval(() => {
        el.style.width = (this.elementWidth - round * 10) + 'px';
        round++;
        if(round > 20){
          clearInterval(interval);
          done();
        }
      }, 20);
    },
    afterLeave(el){
      console.log("after leave");
    },
    leaveCancelled(el){
      console.log("leave cancelled");
    },
    addItem(){
      const pos = Math.floor(Math.random()*this.numbers.length);
      // Add the new number to the array in a random position
      this.numbers.splice(pos, 0, this.numbers.length + 1);
    },
    removeItem(index){
      this.numbers.splice(index, 1);
    }
  },
  components: {
    appDangerAlert: DangerAlert,
    appSuccessAlert: SuccessAlert
  }
}
</script>

<style>
  /* Attached to one from at the beginning - inital state */
  .fade-enter{
    opacity: 0;
  }

  /* Attached to for whole add to element */
  .fade-enter-active{
    transition: opacity 1s;
  }

  .fade-leave{
    /* opacity: 1; */
  }

  /* We ask for the animation to start when the opacity changes from the default which is one */
  .fade-leave-active{
    transition: opacity 1s;
    opacity: 0;
  }

  .slide-enter{
    /* Starting state set up in keyframes below */
    opacity: 0;
  }

  .slide-enter-active{
    animation: slide-in 1s ease-out forwards;
    transition: opacity 1s;
  }

  .slide-leave{

  }

  .slide-leave-active{
    animation: slide-out 1s ease-out forwards;
    transition: opacity 1s;
    opacity: 0;
    position: absolute; 
  }

  .slide-move{
    transition: transform 1s;
  }

  @keyframes slide-in{
    from{
      transform: translateY(20px);
    }
    to{
      transform: translateY(0);
    }
  }

  @keyframes slide-out{
    from{
      transform: translateY(0);
    }
    to{
      transform: translateY(20px);
    }
  }
</style>
