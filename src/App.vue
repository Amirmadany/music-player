<template>

    <main>

        <div class="music">
            <audio src="" ref="audio"></audio>

            <p class="title">Music Player</p>

            <div class="tabs">
                
                <button 
                    v-for="(item, index) in tabs" :key="item + index"
                    class="tab"
                    :class="{active: tab == item}"
                    @click="changeTab(tabs[index])"
                >
                    {{item}}   
                </button>

            </div>

            <MusicPlayer v-if="tab == tabs[1]"
                :song="currentSong"
                :is-playing="isPlaying"
                :song-time="songTime"
                :type-change="typeChangeSong"
                @play="playSong"
                @pause="pauseSong"
                @prev="goToPreviousSong"
                @next="goToNextSong"
                @changeTypePlay="changeTypePlaySong"
                @changeProgressTime="changeProgressTime"
            />

            <MusicList v-else
                :songList="songList"
                :current-song="currentSong"
                @play="playSong"
            />

        </div>

    </main>

</template>

<script>
import { onMounted, reactive, ref } from 'vue'

// components
import MusicPlayer from './components/MusicPlayer.vue'
import MusicList from './components/MusicList.vue'

// load songs
import songs from './songs'

export default {
    components: {
        MusicPlayer,
        MusicList
    },

    setup(){
        // data
        const tabs = ref(['Song List', 'Player'])
        const tab = ref(tabs.value[0])
        const songList = ref(songs)
        const currentSong = ref(songList.value[0])
        const indexSong = ref(0)
        const audio = ref()
        const isPlaying = ref(false)
        const songTime = reactive({
            current: '00:00',
            duration: '00:00'     
        })
        const typeChangeSong = ref('normal')

        // methods
        const changeTab = (newTab) => {
            tab.value = newTab
        }

        const playSong = (song, index) => {
            // if exist song 
            if(typeof song != 'undefined'){
                // set song to current song
                currentSong.value = song
                indexSong.value = index
                audio.value.src = currentSong.value.source
            }

            // play the song
            audio.value.play()

            // go to music player section
            tab.value = tabs.value[1]
            
            // say isPlaying
            isPlaying.value = true
        }

        const pauseSong = () => {
            // pause the song
            audio.value.pause()

            // say is not playing
            isPlaying.value = false
        }

        const goToPreviousSong = () => {
            // if exist previous song come go to the prev song
            if(indexSong.value > 0)
                indexSong.value -= 1
            else
                indexSong.value = songList.value.length - 1

            // set prevouse song to the current song
            currentSong.value = songList.value[indexSong.value]
            audio.value.src = currentSong.value.source

            // play the song
            playSong()
        }

        const goToNextSong = () => {
            // if typeChangeSong be normal come do
            if(typeChangeSong.value == 'normal' || typeChangeSong.value == 'again'){
                // if exist next song come go to next song
                if(indexSong.value < songList.value.length - 1)
                    indexSong.value += 1
                else
                    indexSong.value = 0
            }

            // if typeChange be random come next music by base random
            if(typeChangeSong.value == 'random'){
                // go to next music by base random
                let random = Math.floor(Math.random() * songList.value.length)
                
                // while the choose be current song come retry for finding new random song
                while(random == indexSong.value)
                    random = Math.floor(Math.random() * songList.value.length)

                // go to next song
                indexSong.value = random
            }

            // set new song to the current song
            currentSong.value = songList.value[indexSong.value]
            audio.value.src = currentSong.value.source

            // play the song
            playSong()
        }

        const generateTime = (event) => {
            // give me duration of song
            let durationMin = Math.floor(event.target.duration / 60)
            let durationSec = Math.floor(event.target.duration - durationMin * 60)

            // give me current time of song
            let currentMin = Math.floor(event.target.currentTime / 60)
            let currentSec = Math.floor(event.target.currentTime - currentMin * 60)

            // modify the duration if < 10 with add 0 => 05 insted 5
            if(durationMin < 10)
                durationMin = '0' + durationMin
            
            if(durationSec < 10)
                durationSec = '0' + durationSec

            if(currentMin < 10)
                currentMin = '0' + currentMin
            
            if(currentSec < 10)
                currentSec = '0' + currentSec

            // set time to the songTime
            songTime.duration = `${durationMin}:${durationSec}`
            songTime.current = `${currentMin}:${currentSec}`
        }

        const changeTypePlaySong = (value) => {
            typeChangeSong.value = value
        }

        const changeProgressTime = (value) => {
            audio.value.currentTime = value
        }
 
        // lifecycle hooks - initialiaztion data - events
        onMounted(() => {
            // data init
            audio.value.src = currentSong.value.source

            // when audio loaded
            audio.value.onloadedmetadata = generateTime

            // when updated time of audio come generate time
            audio.value.ontimeupdate = generateTime

            // if audio ended come play next song
            audio.value.onended = () => {
                // if the music be on repetition come play again this song
                if(typeChangeSong.value != 'again') 
                    goToNextSong()
                
                else {
                    audio.value.src = currentSong.value.source
                    playSong()
                }
            }
        })

        return { tabs, tab, songList, audio, currentSong, 
            isPlaying, songTime, typeChangeSong,
            changeTab, playSong, pauseSong, goToPreviousSong,
            goToNextSong, changeTypePlaySong, changeProgressTime
        }
    }
}
</script>

<style src="./assets/main.css"></style>