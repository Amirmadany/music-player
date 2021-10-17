<template>

    <div class="songs">

        <template v-for="(song, index) in songList" :key="song.source">

            <div 
                class="song"
                :class="{active: currentSong.source == song.source}"
                @click="play(song, index)"
            >
            
                <div class="song-cover" :style="`background: url('${song.image}') center/cover no-repeat`"></div>

                <div class="song-detail">
                    <h3 class="name">{{song.name}}</h3>
                    <h4 class="singer">{{song.singer}}</h4>
                </div>

            </div>

        </template>

    </div>

</template>

<script>
export default {
    props: {
        songList: {
            type: Array,
            default: []
        },
        currentSong: Object
    },

    emits: ['play'],

    setup(props, { emit }){
        // methods
        const play = (song, index) => {
            emit('play', song, index)
        }

        return { play }
    }
}
</script>

<style scoped>

    .songs{
        margin: 2rem 0;
    }

    .song{
        box-shadow: 0 0 15px rgba(0, 0, 0, 0.849);
        display: flex;
        align-items: center;
        border-radius: 8px;
        cursor: pointer;
        font-size: 0.9rem;
        margin: 1rem 0 2rem 0;
    }

    .song-cover{
        width: 5rem;
        height: 4.5rem;
        border-radius: 8px 0 0 8px;
         box-shadow: 
        -15px -10px 16px 5px rgba(255, 255, 255, 0.04),
        10px 10px 16px 8px rgb(24, 26, 29);
        margin-right: 1rem;
        margin: 0.3rem 1rem 0.3rem 0.3rem;
    }

    .name{
        margin-bottom: 0.5rem;
    }

    .song.active .song-cover{
        width: 6.4rem;
        height: 5.5rem;
    }

    .song.active{
        font-size: 1rem;
    }

</style>