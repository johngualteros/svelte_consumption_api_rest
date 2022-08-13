<script>
  const { each } = require("svelte/internal");

  let employees = [];
  let active = false;
  let addButtonActive = true;
  let dataEmployees = {
    id: null,
    name: "",
    email: "",
  };

  let showEmployees = () => {
    fetch("http://localhost/empleados/")
      .then((response) => response.json())
      .then((data) => {
        employees = data;
        dataEmployees = {
          id: null,
          name: "",
          email: "",
        };
        console.log(employees);
		active = false;
		addButtonActive = true;
      })
      .catch((error) => {
        console.log(error);
      });
  };

  let handleSubmit = () => {
    const newEmployee = {
      id: dataEmployees.id,
      name: dataEmployees.name,
      email: dataEmployees.email,
    };
    fetch("http://localhost/empleados/?insertar=1", {
      method: "POST",
      body: JSON.stringify(newEmployee),
    }).then(() => {
      showEmployees();
    });
  };
  let deleteEmployee = (id) => {
    fetch(`http://localhost/empleados/?borrar=${id}`, {
      method: "DELETE",
    })
      .then(() => {
        showEmployees();
      })
      .catch((error) => {
        console.log(error);
      });
  };
  let editEmployee = () => {
    fetch(`http://localhost/empleados/?actualizar=${dataEmployees.id}`, {
      method: "PUT",
      body: JSON.stringify(dataEmployees),
    })
      .then(() => {
        showEmployees();
      })
      .catch((error) => {
        console.log(error);
      });
  };
  let updateEmployee = (employee) => {
    dataEmployees = employee;
	active = true;
	addButtonActive = false;
  };
  showEmployees();
</script>

<main
  class="min-h-screen h-auto w-screen bg-zinc-900 text-white p-4 grid grid-cols-2 gap-4"
>
  <!-- Form -->
  <form
    class="rounded-sm shadow-md w-4/5 m-auto p-6 h-3/5"
    on:submit|preventDefault={handleSubmit}
  >
    <h1 class="text-center text-green-500 font-bold text-3xl">Add User</h1>
    <div class="mb-3">
      <label class="block font-bold" for="id">Id</label>
      <input
        class="w-full p-2 shadow-sm bg-transparent border-2 border-green-400 outline-none rounded-md"
        type="number"
        name="id"
        bind:value={dataEmployees.id}
		readonly
      />
    </div>
    <div class="mb-3">
      <label class="block font-bold" for="name">Name</label>
      <input
        class="w-full p-2 shadow-sm bg-transparent border-2 border-green-400 outline-none rounded-md"
        type="text"
        name="name"
        bind:value={dataEmployees.name}
      />
    </div>
    <div class="mb-3">
      <label class="block font-bold" for="email">Email</label>
      <input
        class="w-full p-2 shadow-sm bg-transparent border-2 border-green-400 outline-none rounded-md"
        type="email"
        name="email"
        bind:value={dataEmployees.email}
      />
    </div>
	{#if addButtonActive == true}
    <button
      class="my-4 rounded-md shadow-sm bg-green-600 font-bold hover:bg-green-700 w-full py-2"
      >Save User</button
    >
	{/if}
	{#if active == true}
    <button
      on:click={editEmployee}
      class="my-4 rounded-md shadow-sm bg-sky-600 font-bold hover:bg-sky-700 w-full py-2"
      >Update User</button
    >
	{/if}
	<button
      on:click={showEmployees}
      class="my-4 rounded-md shadow-sm bg-yellow-600 font-bold hover:bg-yellow-700 w-full py-2"
      >Update User</button
    >
  </form>
  <!-- Table -->
  <table class="border-2 border-green-400 h-1/5">
    <thead class="bg-green-400 text-center">
      <tr>
        <th>Id</th>
        <th>Name</th>
        <th>Email</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody class="text-center">
      {#each employees as employee}
        <tr>
          <td>{employee.id}</td>
          <td>{employee.name}</td>
          <td>{employee.email}</td>
          <td>
            <button
              class="bg-lime-400 py-2 px-6 m-2 font-bold rounded-md shadow-md hover:bg-lime-300"
              on:click={updateEmployee(employee)}>Edit</button
            >
            <button
              class="bg-red-500 py-2 px-6 m-2 font-bold rounded-md shadow hover:bg-red-400 transition-all duration-75"
              on:click={deleteEmployee(employee.id)}>Delete</button
            >
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
</main>

<style>
</style>
