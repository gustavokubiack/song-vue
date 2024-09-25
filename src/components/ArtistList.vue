<template>
  <div class="artist-list container">
    <div class="d-flex justify-content-between mb-4">
      <div class="input-group">
        <input
          type="text"
          class="form-control"
          placeholder="Buscar por nome"
          v-model="searchTerm"
        />
      </div>

    <button class="btn text-white" style="background-color: #7C0A02" @click="showModal = true">
      Criar Novo Artista
    </button>
  </div>

    <table class="table table-hover table-bordered">
      <thead class="table-primary">
        <tr>
          <th>#</th>
          <th>Nome</th>
          <th>Nascimento</th>
          <th>Nacionalidade</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(artist, index) in filteredArtists" :key="artist.id">
          <td>{{ index + 1 }}</td>
          <td>{{ artist.name}}</td>
          <td>{{ formatDate(artist.birthday) || '-' }}</td>
          <td>{{ artist.nacionality || '-' }}</td>
        </tr>
      </tbody>
    </table>

    <CreateArtist
      :show="showModal"
      @close="showModal = false"
      @artist-created="addArtist"
    />
  </div>
</template>

<script>
import CreateArtist from '@/components/CreateArtist.vue';
import Swal from 'sweetalert2';

export default {
  components: {
    CreateArtist,
  },
  props: {
    artists: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      searchTerm: '',
      showModal: false,
    };
  },
  computed: {
    filteredArtists() {
      return this.artists.filter((artist) => {
        const lowerSearchTerm = this.searchTerm.toLowerCase();
        return (
          artist.name?.toLowerCase().includes(lowerSearchTerm) ||
          artist.nacionality?.toLowerCase().includes(lowerSearchTerm)
        );
      });
    },
  },
  methods: {
    addArtist(newArtist) {
      this.artists.push(newArtist);
      this.showModal = false;

      Swal.fire({
        icon: 'success',
        title: 'Sucesso',
        text: 'Artista criado com sucesso!',
        confirmButtonText: 'OK',
      });
    },
    formatDate(date) {
      if (!date) return null;
      const options = { year: 'numeric', month: '2-digit', day: '2-digit' };
      return new Date(date).toLocaleDateString(undefined, options);
    },
  },
};
</script>

<style scoped>
.container {
  padding: 20px;
  max-width: 1000px;
  margin: 0 auto;
}

.table {
  margin-bottom: 2rem;
}

.table th {
  background-color: #7C0A02;
  color: white;
  font-weight: bold;
  text-align: center;
}

.table td {
  text-align: center;
  vertical-align: middle;
}

.input-group {
  max-width: 500px;
}

@media (max-width: 768px) {
  .table {
    font-size: 0.9rem;
  }

  .input-group {
    max-width: 100%;
  }
}
</style>
