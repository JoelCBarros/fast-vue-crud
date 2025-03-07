<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-10">
        <h1>Books</h1>
        <hr><br><br>
        <alert v-if="message != ''" :message="message"></alert>
        <!-- eslint-disable-next-line max-len -->
        <button type="button" class="btn btn-success btn-sm" v-b-modal.book-modal @click="formTitle = 'Add a new Book'">
          Add Book</button>
        <br><br>
        <table class="table table-hover">
          <thead>
            <tr>
              <th scope="col">Title</th>
              <th scope="col">Author</th>
              <th scope="col">Read?</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(book, index) in books" :key="index">
              <td>{{ book.title }}</td>
              <td>{{ book.author }}</td>
              <td>
                <span v-if="book.read">Yes</span>
                <span v-else>No</span>
              </td>
              <td>
                <div class="btn-group" role="group">
                  <!-- eslint-disable-next-line max-len -->
                  <button type="button" class="btn btn-warning btn-sm" v-b-modal.book-modal
                    @click="editBook(book)">Update</button>
                  <!-- eslint-disable-next-line max-len -->
                  <button type="button" class="btn btn-danger btn-sm" @click="onDeleteBook(book)">
                    Delete
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <b-modal ref="bookModal" id="book-modal" :title="formTitle" hide-footer>
      <b-form @submit="onSubmit" @reset="onReset" class="w-100">
        <b-form-group id="form-title-group" label="Title:" label-for="form-title-input">
          <!-- eslint-disable-next-line max-len -->
          <b-form-input id="form-title-input" type="text" v-model="bookForm.title" required placeholder="Enter title">
          </b-form-input>
        </b-form-group>
        <b-form-group id="form-author-group" label="Author:" label-for="form-author-input">
          <b-form-input id="form-author-input" type="text" v-model="bookForm.author" required
            placeholder="Enter author">
          </b-form-input>
        </b-form-group>
        <b-form-group id="form-read-group">
          <b-form-checkbox-group v-model="bookForm.read" id="form-checks">
            <b-form-checkbox value="true">Read?</b-form-checkbox>
          </b-form-checkbox-group>
        </b-form-group>
        <b-button type="submit" variant="primary">Submit</b-button>
        <b-button type="reset" variant="danger">Reset</b-button>
      </b-form>
    </b-modal>
  </div>
</template>

<script>
import axios from 'axios';
import Alert from './Alert.vue';

export default {
  data() {
    return {
      books: [],
      bookForm: {
        id: '',
        title: '',
        author: '',
        read: [],
      },
      isUpdate: false,
      message: '',
      formTitle: '',
    };
  },
  components: {
    alert: Alert,
  },

  methods: {
    getBooks() {
      const path = 'http://localhost:8000/books';
      axios.get(path)
        .then((res) => {
          this.books = res.data.books;
          if (this.books.length === 0) {
            this.message = 'No books! Please add one';
          }
        })
        .catch((error) => {
          console.error(error);
        });
    },
    addBook(payload) {
      const path = 'http://localhost:8000/books';
      axios.post(path, payload)
        .then(() => {
          this.getBooks();
          this.message = 'Book Added!';
        })
        .catch((error) => {
          console.log(error);
          this.getBooks();
        });
    },
    updateBook(payload, bookID) {
      const path = `http://localhost:8000/books/${bookID}`;
      axios.put(path, payload)
        .then(() => {
          this.getBooks();
          this.message = 'Book updated!';
          this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getBooks();
        });
    },
    editBook(book) {
      this.message = '';
      this.bookForm = book;
      this.formTitle = 'Update Book';
      this.isUpdate = true;
    },
    removeBook(bookID) {
      const path = `http://localhost:8000/books/${bookID}`;
      axios.delete(path)
        .then(() => {
          this.getBooks();
          this.message = 'Book removed!';
        })
        .catch((error) => {
          console.error(error);
          this.getBooks();
        });
    },
    onDeleteBook(book) {
      this.message = '';
      this.$bvModal.msgBoxConfirm('Are you sure?')
        .then((value) => {
          if (value) {
            this.removeBook(book.id);
          }
        })
        .catch((error) => {
          console.error(error);
          this.getBooks();
        });
    },
    initForm() {
      this.bookForm.title = '';
      this.bookForm.author = '';
      this.bookForm.read = [];
      this.message = '';
    },
    onSubmit(evt) {
      evt.preventDefault();
      this.$refs.bookModal.hide();
      let read = false;
      if (this.bookForm.read[0]) read = true;
      const payload = {
        title: this.bookForm.title,
        author: this.bookForm.author,
        read,
      };
      if (this.isUpdate) {
        this.updateBook(payload, this.bookForm.id);
        this.isUpdate = false;
      } else {
        this.addBook(payload);
      }
      this.initForm();
    },
    onReset(evt) {
      evt.preventDefault();
      this.$refs.bookModal.hide();
      this.initForm();
      this.getBooks();
    },
  },
  created() {
    this.getBooks();
  },
};
</script>
