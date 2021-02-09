<template>
  <section class="mt-5">
    <div class="container mb-4">
      <div class="row">
        <div class="col-md-12">
          <div class="card">
            <div class="card-body">
              <div class="card-title mb-4">
                <h4>Add Music</h4>
              </div>
              <form>
                <div class="form-group">
                  <label for="title">Title</label>
                  <input type="text" class="form-control" />
                </div>
                <div class="form-group">
                  <label for="artist">Artist</label>
                  <input type="text" class="form-control" />
                </div>
                <div class="form-group">
                  <label for="artist">Music</label>
                  <div class="custom-file">
                    <input
                      type="file"
                      class="custom-file-input"
                      id="customFile"
                    />
                    <label class="custom-file-label" for="customFile"
                      >Choose file</label
                    >
                  </div>
                </div>
                <div class="form-group">
                  <button class="btn btn-primary">Submit</button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="row">
        <div class="col-md-12">
          <div class="card bg-light p-1 showdow-sm">
            <div class="card-title">
              <button class="btn btn-info m-3">Add Music</button>
            </div>
            <div class="card-body">
              <table class="table">
                <thead>
                  <tr>
                    <th scope="col">#</th>
                    <th scope="col">Title</th>
                    <th scope="col">Artist</th>
                    <th scope="col">Date created</th>
                    <th scope="col">Action</th>
                  </tr>
                </thead>
                <tbody>
                  <tr>
                    <td>1</td>
                    <td>Demo Title</td>
                    <td>Wisdom.vue</td>
                    <td>12/23/13</td>
                    <td>
                      <button class="btn btn-info">Delete</button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>