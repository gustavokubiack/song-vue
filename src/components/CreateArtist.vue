<template>
  <div v-if="show" class="modal" tabindex="-1" role="dialog">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Criar Novo Artista</h5>
        </div>
        <div class="modal-body">
          <form @submit.prevent="handleCreateArtist">
            <div class="form-group mb-3">
              <label for="artist-name">Nome</label>
              <input
                type="text"
                id="artist-name"
                class="form-control"
                v-model="artist.name"
                placeholder="Nome do artista"
                required
              />
            </div>
            <div class="form-group mb-3">
              <label for="artist-birthday">Data de Nascimento</label>
              <input
                type="date"
                id="artist-birthday"
                class="form-control"
                v-model="artist.birthday"
              />
            </div>
            <div class="form-group mb-3">
              <label for="artist-nacionality">Nacionalidade</label>
              <input
                type="text"
                id="artist-nacionality"
                class="form-control"
                v-model="artist.nacionality"
                placeholder="Nacionalidade do artista"
              />
            </div>
            <button type="button" class="btn btn-secondary" @click="closeModal">Fechar</button>
            <button type="submit" class="btn btn-primary">Criar Artista</button>
          </form>
        </div>
        <div class="modal-footer">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { createArtist } from '@/api/artists';
import Swal from 'sweetalert2';

export default {
  props: {
    show: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      artist: {
        name: '',
        birthday: '',
        nacionality: '',
      },
    };
  },
  methods: {
    async handleCreateArtist() {
      try {
        const artistData = {
          name: this.artist.name,
          birthday: this.artist.birthday === '' ? null : this.artist.birthday,
          nacionality: this.artist.nacionality,
        };

        const newArtist = await createArtist(
          artistData.name,
          artistData.birthday,
          artistData.nacionality
        );
        this.$emit('artist-created', newArtist);

        this.closeModal();
      } catch (error) {
        Swal.fire({
          icon: 'error',
          title: 'Erro',
          text: 'Erro ao criar artista. Tente novamente.',
        });
      }
    },
    closeModal() {
      this.$emit('close');
    },
  },
};
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
