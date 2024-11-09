<template>
  <q-page padding class="flex flex-center ">
    <div class="MyClass">
      <h4>Adicionar contatos</h4>
    <q-form @submit.prevent="isEditing ? updateContact() : addContact()">
      <q-input v-model="contact.name" label="Name" outlined class="q-my-sm" />
      <q-input v-model="contact.phone" label="Phone" outlined class="q-my-sm" />
      <q-input v-model="contact.email" label="Email" outlined class="q-my-sm" />
      <q-btn :label="isEditing ? 'Update Contact' : 'Add Contact'" type="submit" color="primary" class="q-my-md" />
      <q-btn v-if="isEditing" label="Cancel" color="negative" flat @click="cancelEdit" />
    </q-form>

    <h4>Contatos</h4>
    <div class="q-pa-md q-my-md">
      <q-select
        v-model="selectedLetter"
        :options="alphabet"
        label="Filter by first letter"
        outlined
        clearable
        class="q-my-md"
      />
    </div>

    <q-table
      :rows="filteredContacts"
      :columns="columns"
      row-key="name"
      :pagination.sync="pagination"
      hide-bottom
      class="q-my-md"
    >
      <template v-slot:body-cell-actions="props">
        <q-td align="center">
          <q-btn flat icon="edit" color="primary" @click="editContact(props.row, props.rowIndex)" />
          <q-btn flat icon="delete" color="negative" @click="removeContact(props.rowIndex)" />
        </q-td>
      </template>
    </q-table>

    <q-pagination
      v-model="pagination.page"
      :max="pages"
      max-pages="5"
      color="primary"
      boundary-numbers
      class="q-my-md flex flex-center"
      
    />
  </div>
  </q-page>
</template>

<script>
export default {
  data() {
    return {
      contact: {
        name: '',
        phone: '',
        email: ''
      },
      contacts: JSON.parse(localStorage.getItem('contacts')) || [],
      selectedLetter: null,
      alphabet: [...'ABCDEFGHIJKLMNOPQRSTUVWXYZ'],
      pagination: {
        page: 1,
        rowsPerPage: 5
      },
      columns: [
        { name: 'name', label: 'Name', align: 'left', field: 'name' },
        { name: 'phone', label: 'Phone', align: 'left', field: 'phone' },
        { name: 'email', label: 'Email', align: 'left', field: 'email' },
        { name: 'actions', label: 'Actions', align: 'center' }
      ],
      isEditing: false,
      editIndex: null
    };
  },
  computed: {
    filteredContacts() {
      const filtered = this.selectedLetter
        ? this.contacts.filter(contact => contact.name.startsWith(this.selectedLetter))
        : this.contacts;

      const start = (this.pagination.page - 1) * this.pagination.rowsPerPage;
      const end = start + this.pagination.rowsPerPage;

      return filtered.slice(start, end);
    },
    pages() {
      const filtered = this.selectedLetter
        ? this.contacts.filter(contact => contact.name.startsWith(this.selectedLetter))
        : this.contacts;
      return Math.ceil(filtered.length / this.pagination.rowsPerPage);
    }
  },
  methods: {
    addContact() {
      if (this.contact.name && this.contact.phone && this.contact.email) {
        this.contacts.push({ ...this.contact });
        localStorage.setItem('contacts', JSON.stringify(this.contacts));
        this.contact = { name: '', phone: '', email: '' };
      }
    },
    editContact(contact, index) {
      this.isEditing = true;
      this.editIndex = index;
      this.contact = { ...contact };
    },
    updateContact() {
      if (this.contact.name && this.contact.phone && this.contact.email) {
        this.contacts.splice(this.editIndex, 1, { ...this.contact });
        localStorage.setItem('contacts', JSON.stringify(this.contacts));
        this.cancelEdit();
      }
    },
    cancelEdit() {
      this.isEditing = false;
      this.editIndex = null;
      this.contact = { name: '', phone: '', email: '' };
    },
    removeContact(index) {
      this.contacts.splice(index, 1);
      localStorage.setItem('contacts', JSON.stringify(this.contacts));
    }
  }
};
</script>
