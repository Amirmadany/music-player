<template>

    <div class="player">

        <div class="music-cover-container">
            <div class="music-cover" :style="`background: url('${song.image}') center/cover no-repeat;`"></div>
        </div>

        <h1 class="song-name">{{song.name}}</h1>
        <h2 class="singer-name">{{song.singer}}</h2>

        <div class="seek-song">

            <div class="song-time">
                <span>{{songTime.current}}</span>
                <span>{{songTime.duration}}</span>
            </div>

            <div class="seek-bar" @click="changeProgressPercentage">
                <div class="seek-progress" ref="progress"></div>
            </div>

        </div>

        <div class="music-player-controls">
            
            <button @click="changeTypePlaySong('random')" class="action-button" :class="{active: typeChange == 'random'}">
                <div class="inner-action-button">
                    <i class="fas fa-random"></i>
                </div>
            </button>

            <button @click="prev" class="control-button">
                
                <div class="control-button-innner">
                    <i class="fas fa-backward"></i>
                </div>

            </button>

            <button @click="togglePlay" class="control-button play-pause-btn">

                <div class="control-button-innner">
                    <span v-if="!isPlaying">
                        <i class="fas fa-play"></i>
                    </span>

                    <span v-else>
                        <i class="fas fa-pause"></i>
                    </span>
                </div>

            </button>

            <button @click="next" class="control-button">
                
                <div class="control-button-innner">
                    <i class="fas fa-forward"></i>
                </div>

            </button>

            <button @click="changeTypePlaySong('again')" class="action-button" :class="{active: typeChange == 'again'}">
               
                <div class="inner-action-button">
                    <i class="fas fa-sync-alt"></i>
                </div>

            </button>

        </div>

    </div>

</template>

<script>
import { ref, watch } from 'vue'

export default {
    props: {
        song: Object,
        isPlaying: Boolean,
        songTime: Object,
        typeChange: {
            type: String,
            default: 'normal'
        }
    },

    emits: ['play', 'pause', 'prev', 'next', 'changeTypePlay', 'changeProgressTime'],

    setup(props, { emit }){
        // data
        const progress = ref()

        // methods
        const togglePlay = () => {
            // if music is playing come pause 
            if(props.isPlaying)
                pause()
            else    
                play()            
        }

        const play = () => {
            emit('play')
        }

        const pause = () => {
            emit('pause')
        }

        const prev = () => {
            emit('prev')
        }

        const next = () => {
            emit('next')
        }

        const changeTypePlaySong = (type) => {
            // if type change be again the same come disable type change and back to default change
            if(props.typeChange == type)
                type = 'normal'

            emit('changeTypePlay', type)
        }

        const explodeTime = () => {
            // explode : from the time song and get seconds
            let currentTime = props.songTime.current.split(':')
            currentTime = (Number(currentTime[0]) * 60) + (Number(currentTime[1]))

            let duration = props.songTime.duration.split(':')
            duration = (Number(duration[0]) * 60) + Number(duration[1])

            return {
                currentTime, 
                duration
            }
        }

        const changeProgressPercentage = (event) => {
            const width = event.target.clientWidth

            // give me desired point
            const setPoint = event.offsetX

            // explode time - give me time
            const { duration } = explodeTime()

            // calculate position selected by user
            const point = (setPoint / width) * duration

            emit('changeProgressTime', point)
        }

        // watch
        watch(props, () => {
            // explode : from the songTime
            const time = explodeTime()
            
            // calculate progress song
            const percentage = (Number(time.currentTime) / Number(time.duration)) * 100

            progress.value.style.width = `${percentage}%`
        })

        return { togglePlay, prev, next, progress, changeTypePlaySong, changeProgressPercentage }
    }
}
</script>

<style src="./MusicPlayer.css"></style>