<template>
  <div>
    <p>Aqui você pode gerenciar suas avaliações das músicas.</p>

    <!-- Tabela de Reviews -->
    <table class="table">
      <thead>
        <tr>
          <th>#</th>
          <th>Música</th>
          <th>Avaliação</th>
          <th>Comentário</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(review, index) in reviews" :key="review.id">
          <td>{{ index + 1 }}</td>
          <td>{{ review.song.title }}</td>
          <td>{{ review.stars }}</td>
          <td>{{ review.comment }}</td>
          <td class="d-flex justify-content-start">
            <button class="btn btn-warning me-2" @click="openEditModal(review)">Editar</button>
            <button class="btn btn-danger" @click="deleteReview(review.id)">Excluir</button>
          </td>
        </tr>
      </tbody>
    </table>

    <div v-if="showEditModal" class="modal" tabindex="-1" role="dialog">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Editar Review</h5>
          </div>
          <div class="modal-body">
            <form @submit.prevent="handleEditReview">
              <div class="form-group">
                <label for="editComment">Comentário</label>
                <textarea
                  id="editComment"
                  class="form-control"
                  v-model="selectedReview.comment"
                  required
                ></textarea>
              </div>
              <div class="form-group">
                <label for="editRating">Nota (1 a 5 estrelas)</label>
                <input
                  type="number"
                  id="editRating"
                  class="form-control"
                  v-model="selectedReview.stars"
                  min="1"
                  max="5"
                  required
                />
              </div>
              <div class="d-flex justify-content-end mt-2">
                <button type="button" class="btn btn-secondary" @click="closeEditModal">
                  Cancelar
                </button>
                <button type="submit" class="btn btn-primary ms-2">Salvar</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { fetchReviewsByUser, updateReview, deleteReview } from '@/api/reviews'
import Swal from 'sweetalert2'

export default {
  data() {
    return {
      reviews: [],
      showEditModal: false,
      selectedReview: null
    }
  },
  async created() {
    try {
      this.reviews = await fetchReviewsByUser()
    } catch (error) {
      console.error('Erro ao carregar reviews:', error)
    }
  },
  methods: {
    openEditModal(review) {
      this.selectedReview = { ...review }
      this.showEditModal = true
    },
    closeEditModal() {
      this.showEditModal = false
      this.selectedReview = null
    },
    async handleEditReview() {
      try {
        await updateReview(this.selectedReview)
        this.showEditModal = false
        this.selectedReview = null
        this.reviews = await fetchReviewsByUser()
      } catch (error) {
        console.error('Erro ao editar review:', error)
      }
    },
    async deleteReview(reviewId) {
      try {
        const result = await Swal.fire({
          title: 'Tem certeza?',
          text: 'Você não poderá reverter essa ação!',
          icon: 'warning',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Sim, excluir!',
          cancelButtonText: 'Cancelar'
        })

        if (result.isConfirmed) {
          await deleteReview(reviewId)
          this.reviews = this.reviews.filter((review) => review.id !== reviewId)

          Swal.fire({
            title: 'Excluída!',
            text: 'A review foi excluída com sucesso.',
            icon: 'success'
          })
        }
      } catch (error) {
        console.error('Erro ao excluir review:', error)
        Swal.fire({
          title: 'Erro',
          text: 'Houve um erro ao excluir a review. Tente novamente.',
          icon: 'error'
        })
      }
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
