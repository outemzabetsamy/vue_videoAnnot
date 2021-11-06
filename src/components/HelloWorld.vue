<template>
 <fullscreen :fullscreen="isFullScreen" :teleport="teleport" :pageOnly="pageOnly">
  <div class="flat-html5-player-wrapper fullscreen-wrapper">
        <div class="flat-player" id="player">
         
            <div class="flat-video-preview-wrapper pointer">
               <div v-if="arrowClicked" class="annot" @click="getClients">
                   <annot v-for="(annot,ind) in annotationsChoosed" :key="ind" :xleft="annot.left" :ytop="annot.top"></annot>
          </div>
          <div class="vi">
              <video :src="video.url" ref="videoElem" class="flat-video pointer" @pause="getTime"></video>
          </div>
                
            </div>
            <div class="flat-video-footer">
                <div class="flat-video-annotation-position pointer">
                   <arrow v-for="(arrow,ind) in annotationGroupedByTime" :key="ind" :arrowLeft="getLeftArrow(arrow.time)" @click.native="skipVideoArrow(arrow.time,ind)"></arrow>
                </div>
                <div class="flat-video-progressbar pointer" @click="skipVideo">
                    <div :style="{width: progressPercentage() + '%'}" class="flat-video-current-progress pointer"></div>
                </div>

                <div class="flat-video-controls">
                    <div class="flat-video-controls-left">
                        <div class="">
                            <button v-if="isPaused()" @click="play" class="flat-video-control-single">
                               <!-- <i class="fa fa-fw fa-play"></i>-->
                                <font-awesome-icon icon="play" />
                            </button>
                            <button v-if="!isPaused()" @click="pause" class="flat-video-control-single">
                               <!-- <i class="fa fa-fw fa-pause"></i>-->
                                <font-awesome-icon icon="pause" />
                            </button>
                        </div>
                        <div class="flat-video-volume-rocker">
                            <button @click="muteToggle" v-if="isMuted()" class="flat-video-control-single">
                                <!--<i class="fa fa-fw fa-volume-off"></i>-->
                                <font-awesome-icon icon="volume-off" />
                            </button>
                            <button @click="muteToggle" v-if="!isMuted()" class="flat-video-control-single">
                               <!-- <i class="fa fa-fw fa-volume-up"></i>-->
                                <font-awesome-icon icon="volume-up" />
                            </button>
                            <div class="flat-video-volume-progress pointer" @click="changeVolume">
                                <div :style="{width: volume() + '%'}"
                                    class="flat-video-volume-progress-current pointer"></div>
                            </div>
                        </div>
                    </div>
                    <div class="flat-video-controls-right">
                        <div class="flat-video-time-container">
                            <span
                                class="flat-video-current-time">{{formatTime(currentTime())}}</span><span>/</span><span
                                class="flat-video-total-time">{{formatTime(duration())}}</span>
                        </div>
                        <div class="flat-video-buttons-right-wrapper">
                            <button v-if="isFullScreen" @click="compressFullScreen" class="flat-video-control-single">
                               <!-- <i class="fa fa-fw fa-compress"></i>-->
                                <font-awesome-icon icon="compress" />
                            </button>
                            <button v-if="!isFullScreen" @click="toggleFullScreen" class="flat-video-control-single">
                                <!--<i class="fa fa-fw fa-expand"></i>-->
                                <font-awesome-icon icon="expand" />
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
 </fullscreen>
    
</template>


<script>
import Vue from 'vue'
import VueFullscreen from 'vue-fullscreen'
import Annot from './Annot.vue'
import ArrowPositionAnnot from './ArrowPositionAnnot.vue'
Vue.use(VueFullscreen)

export default {
  name: 'HelloWorld',
  components:{
    "annot":Annot,
    "arrow":ArrowPositionAnnot,
  },
  props: {
    msg: String
  },
  data() {
    return{
        video: {
            url: "http://localhost:8089/video/video.mp4"
        },
        isFullScreen: false,
        teleport: true,
        pageOnly: false,
        paused:false,
        arrowClicked:false,
        lastPausedTime:null,
        annotationsChoosed:null,
        annotaion:[
            {
                idAnnot:1,
                timeAnnot:50,
                left:100,
                top:100,
                textAnnot:"TEST3",
                idVideo:"dffvfvgf"
            },
            {
                idAnnot:1,
                timeAnnot:120,
                left:200,
                top:200,
                textAnnot:"TEST3",
                idVideo:"dffvfvgf"
            },
            {
                idAnnot:1,
                timeAnnot:300,
                left:300,
                top:300,
                textAnnot:"TEST3",
                idVideo:"dffvfvgf"
            },
            ],
            annotationGroupedByTime:[{
                id:1,
                time:50,
                idVideo:"kidjdjvdjvf",
                annotations:
                [
                    {
                idannota:1,
                text:"annooot",
                left:100,
                top:100,

                },
                 {
                idannota:2,
                text:"annooot2",
                left:200,
                top:200,

                },
                {
                idannota:3,
                text:"annooot3",
                left:300,
                top:300,

                },
                
                
                
                ]
            },
            {
                id:2,
                time:100,
                idVideo:"kidjdjvdjvf",
                annotations:
                [
                    {
                idannota:1,
                text:"annooot",
                left:70,
                top:20,

                },
                 {
                idannota:2,
                text:"annooot2",
                left:65,
                top:200,

                },
                {
                idannota:3,
                text:"annooot3",
                left:254,
                top:300,

                },
                
                
                
                ]
            },
            {
                id:3,
                time:200,
                idVideo:"kidjdjvdjvf",
                annotations:
                [
                    {
                idannota:1,
                text:"annooot",
                left:40,
                top:49,

                },
                 {
                idannota:2,
                text:"annooot2",
                left:321,
                top:26,

                },
                {
                idannota:3,
                text:"annooot3",
                left:54,
                top:64,

                },
                
                
                
                ]
            }]
        
    }
    },

    mounted: function () {

        let events = [
            'timeupdate',
            'volumechange',
            'seeked',
            'loadedmetadata'
        ];

        events.map(e => {
            this.$refs.videoElem.addEventListener(e, () => {
                this.$forceUpdate();
            });
        });




        this.$refs.videoElem.addEventListener('loadedmetadata', () => {
            this.$refs.videoElem.volume = 0.3;
            this.$forceUpdate();
        });

        this.$refs.videoElem.addEventListener('click', () => {
            if (this.isPaused()) {
                this.play();
            } else {
                this.pause();
            }
        });

        this.$refs.videoElem.addEventListener('dblclick', () => {
            this.toggleFullScreen();
        });







    },

    methods: {
        getClients:function(e){
            console.log(e);
            console.log(document.querySelector(".oneAnnot"));
            const ExistingTime=((a)=>a.time==this.lastPausedTime)
            let ind=this.annotationGroupedByTime.findIndex(ExistingTime);
            console.log(ind)
            if(ind == -1){
                console.log("ind ==-1")
                this.annotationGroupedByTime.push(
                     {
                id:3,
                time:this.lastPausedTime,
                idVideo:"kidjdjvdjvf",
                annotations:
                [{
                idannota:1,
                text:"annooot",
                left:e.layerX,
                top:e.layerY,
                },]
            }
                )
            }else{
                this.annotationGroupedByTime[ind].annotations.push({
                idannota:1,
                text:"annooot",
                left:e.layerX,
                top:e.layerY,
                })
            }
            
        },
        getLeftArrow: function( time) {
            return time/ this.duration() * 100
        },

        muteToggle: function () {
            this.$refs.videoElem.muted = !this.$refs.videoElem.muted;
        },

        isMuted: function () {
            return this.$refs.videoElem ? this.$refs.videoElem.muted : false;
        },

        isPaused: function () {
            return this.$refs.videoElem ? this.$refs.videoElem.paused : true;
        },

        play: function () {
            this.$refs.videoElem.play();
            this.arrowClicked=false;
        },

        pause: function () {
            this.$refs.videoElem.pause();
            this.annotationsChoosed=null;
            this.arrowClicked=true;
        },

        currentTime: function () {
            return this.$refs.videoElem?.currentTime || 0;
        },

        duration: function () {
            return this.$refs.videoElem?.duration || 0;
        },

        progressPercentage: function () {
            return (this.currentTime() / this.duration()) * 100;
        },

        formatTime: function (time) {
            if (!time || !parseInt(time)) {
                return `00:00`;
            }

            let hours, minutes, seconds;
            minutes = Math.floor(((time / 60) % 60)),
                seconds = Math.floor(time % 60),
                hours = Math.floor(time / 60 / 60);

            if (minutes < 10) minutes = `0${minutes}`;
            if (seconds < 10) seconds = `0${seconds}`;

            return `${hours ? hours + ':' : ''}${minutes}:${seconds}`;
        },

        skipVideo: function (event) {
            let wrapper_offset = event.currentTarget.getBoundingClientRect().left;
            let clicked_offset = event.clientX - wrapper_offset;

            let progress_width = (clicked_offset / event.currentTarget.getBoundingClientRect().width) * 100;
            let newTime = (this.duration() / 100) * progress_width;

            this.$refs.videoElem.currentTime = newTime;
            
        },
        skipVideoArrow: function(time,ind){
            this.$refs.videoElem.currentTime = time;
            this.pause();
            this.lastPausedTime=time;
            this.arrowClicked=true;
            this.annotationsChoosed=this.annotationGroupedByTime[ind].annotations;
        },
        toggleFullScreen: function () {
            //fu.toggle();
            let s=document.querySelector(".flat-player");
            console.log(s.style)
            s.style.setProperty('--height','100%')
            s.style.setProperty('--width','100%')
            this.isFullScreen = !this.isFullScreen;
            this.$forceUpdate();
        },
         compressFullScreen: function () {
            //fu.toggle();
            let s=document.querySelector(".flat-player");
            console.log(s.style)
            s.style.setProperty('--height','600px')
            s.style.setProperty('--width','600px')
            this.isFullScreen = !this.isFullScreen;
            this.$forceUpdate();
        },

        changeVolume: function (event) {
            let wrapper_offset = event.currentTarget.getBoundingClientRect().left;
            let clicked_offset = event.clientX - wrapper_offset;
            let volume_bar_width = (clicked_offset / event.currentTarget.getBoundingClientRect().width) * 100;
            this.$refs.videoElem.volume = volume_bar_width / 100;
        },

        volume: function () {
            return this.$refs.videoElem ? this.$refs.videoElem.volume * 100 : 0;
        },
        started(e){
      console.log(e);
    },
    getTime(e){
        this.paused=true;
        console.log("TIIIIIIIIIIIIIIIIIIIIIIIIIIME")
      console.log(e.target.currentTime)
      this.lastPausedTime=e.target.currentTime;
    }
    },
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style src="./main.css">

</style>
