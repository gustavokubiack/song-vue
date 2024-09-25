<template>
  <div v-if="show" class="modal" tabindex="-1" role="dialog">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Criar Review</h5>
        </div>
        <div class="modal-body">
          <form @submit.prevent="handleCreateReview">
            <div class="form-group mb-3">
              <label for="song-select">Música</label>
              <select id="song-select" class="form-control" v-model="review.song_id" required>
                <option v-for="song in songs" :key="song.id" :value="song.id">
                  {{ song.title }}
                </option>
              </select>
            </div>

            <div class="form-group mb-3">
              <label for="review-comment">Comentário</label>
              <textarea
                id="review-comment"
                class="form-control"
                v-model="review.comment"
                placeholder="Escreva seu comentário"
                required
              ></textarea>
            </div>

            <div class="form-group mb-3">
              <label for="review-rating">Nota (1 a 5 estrelas)</label>
              <input
                type="number"
                id="review-rating"
                class="form-control"
                v-model="review.rating"
                min="1"
                max="5"
                required
              />
            </div>

            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" @click="closeModal">Fechar</button>
              <button type="submit" class="btn btn-primary">Criar Review</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { createReview } from '@/api/reviews'
import Swal from 'sweetalert2'
import { getUserInfoFromToken } from '@/api/auth'

export default {
  props: {
    show: {
      type: Boolean,
      required: true
    },
    songs: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      review: {
        song_id: null,
        comment: '',
        rating: 1
      },
      userInfo: null,
      song: []
    }
  },
  methods: {
    async handleCreateReview() {
      this.userInfo = await getUserInfoFromToken()
      try {
        const reviewData = {
          song: this.review.song_id,
          comment: this.review.comment,
          stars: this.review.rating,
          user: this.userInfo.userId
        }
        const newReview = await createReview(reviewData)
        this.$emit('review-created', newReview)
        this.closeModal()
      } catch (error) {
        console.log(error)
        Swal.fire({
          icon: 'error',
          title: 'Erro',
          text: 'Erro ao criar review. Tente novamente.'
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
