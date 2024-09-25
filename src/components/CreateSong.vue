<template>
  <div v-if="show" class="modal" tabindex="-1" role="dialog">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Criar Nova Música</h5>
        </div>
        <div class="modal-body">
          <form @submit.prevent="handleCreateSong">
            <div class="form-group mb-3">
              <label for="song-title">Título</label>
              <input
                type="text"
                id="song-title"
                class="form-control"
                v-model="song.title"
                placeholder="Título da música"
                required
              />
            </div>
            <div class="form-group mb-3">
              <label for="song-release">Data de Lançamento</label>
              <input
                type="date"
                id="song-release"
                class="form-control"
                v-model="song.release_date"
              />
            </div>
            <div class="form-group mb-3">
              <label for="song-genre">Gênero</label>
              <select id="song-genre" class="form-control" v-model="song.genre_id">
                <option v-for="genre in genres" :key="genre.id" :value="genre.id">
                  {{ genre.name }}
                </option>
              </select>
            </div>
            <div class="form-group mb-3">
              <label for="song-artists">Artistas</label>
              <select id="song-artists" class="form-control" v-model="selectedArtists" multiple>
                <option v-for="artist in artists" :key="artist.id" :value="artist.id">
                  {{ artist.name }}
                </option>
              </select>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" @click="closeModal">Fechar</button>
              <button type="submit" class="btn btn-primary">Criar Música</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { createSong } from '@/api/songs'
import Swal from 'sweetalert2'

export default {
  props: {
    show: {
      type: Boolean,
      required: true
    },
    genres: {
      type: Array,
      required: true
    },
    artists: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      song: {
        title: '',
        release_date: '',
        genre_id: null
      },
      selectedArtists: []
    }
  },
  methods: {
    async handleCreateSong() {
      try {
        const songData = {
          title: this.song.title,
          release_date: this.song.release_date === '' ? null : this.song.release_date,
          genre: this.song.genre_id,
          artists: this.selectedArtists
        }
        console.log(songData)
        const newSong = await createSong(songData)

        this.$emit('song-created', newSong)
        this.closeModal()
      } catch (error) {
        Swal.fire({
          icon: 'error',
          title: 'Erro',
          text: 'Erro ao criar música. Tente novamente.'
        })
      }
    },
    closeModal() {
      this.$emit('close')
    }
  }
}
</script>

<style scoped>
.modal {
  display: block;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-dialog {
  max-width: 500px;
  margin: 1.75rem auto;
}

.modal-content {
  border-radius: 5px;
}
</style>
