<script lang="ts">
  let filter : string = $state("all");
  let date : string = $state("");
  let inputValue : string = $state("");
  let tasks : string[] = $state([]);
  let dates : string[] = $state([]);
  let isChecked : boolean[] = $state([]);
  let isMod : boolean[] = $state([]);
  let color : string[] = $state([]);
  let border : string[] = $state([]);
  let img = 'src/routes/img/pencil.png';

  function addTask(){
      if(inputValue != "" && date != ""){
          formatDate();
          isMod[isMod.length] = false;
          isChecked[isChecked.length] = false;
          color[color.length] = "bg-gray-100";
          tasks[tasks.length] = inputValue;
          dates[dates.length] = date;
          date = "";
          inputValue = "";
      }
  }

  function formatDate(){
      let dateObj = new Date(date);
      let month = (dateObj.getMonth() + 1).toString().padStart(2, '0');
      let day = dateObj.getDate().toString().padStart(2, '0');
      let year = dateObj.getFullYear().toString();
      date = `${day}/${month}/${year}`;
  }

  function deleteTask(index : number){
      tasks = tasks.filter((_, i) => i !== index);  
      dates = dates.filter((_, i) => i !== index);
  }

  function isComplete(index : number){
      if(isChecked[index] != false){
          color[index] = "bg-green-600";
      }else{
          color[index] = "bg-gray-100";
      }
  }

  function filterTasks(){
      return tasks.map((task, i) => ({ task, index: i }))
                  .filter(({ index }) => 
                      filter === "all" ||
                      (filter === "completed" && isChecked[index]) ||
                      (filter === "pending" && !isChecked[index])
              );
  }
  
  function modTask(index : number){
      if(isMod[index] == false){
          border[index] = "flex-grow p-1 border rounded-l-md";
          isMod[index] = true;
      }else{
          isMod[index] = false;
          border[index] = "";
      }
  }
</script>

<div class="w-full max-w-6xl mx-auto mt-10 p-4 bg-white rounded-lg shadow-md">
<h2 class="text-2xl font-bold mb-4 text-center">Lista Attività</h2>

<div class="flex items-center gap-2 mb-6">
  <input
    bind:value={inputValue}
    type="text"
    maxlength=22
    placeholder="Aggiungi un'attività..."
    class="flex-grow p-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400"
    onkeydown={(e) => e.key === "Enter" && addTask()}
  />
  <input
    bind:value={date}
    type="date"
    class="p-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400"
  />
  <button
    onclick={addTask}
    class="bg-blue-600 text-white px-4 py-2 rounded-md hover:bg-blue-700"
  >Aggiungi</button>

  <div class="flex items-center gap-2 ml-4">
    <span class="text-3xl font-bold">Filtra:</span>
    <select bind:value={filter} class="p-2 border rounded-md mt-[2px] focus:outline-none focus:ring-2 focus:ring-blue-400">
      <option value="all">Tutte</option>
      <option value="completed">Completate</option>
      <option value="pending">Non completate</option>
    </select>
  </div>
</div>

<ul class="space-y-2">
  {#each filterTasks() as { task, index }}
  <li class="flex justify-between items-center p-3 {color[index]} rounded-md">
    <div class="flex gap-x-2 w-full">
      <span contenteditable={isMod[index]} class={border[index]}>{task}</span>
      <div class="flex justify-end gap-x-2 ml-auto">
        <span contenteditable={isMod[index]} class="w-32 text-right {border[index]}">{dates[index]}</span>
        <input 
            bind:checked={isChecked[index]}
            onchange={() => isComplete(index)}
            type="checkbox" 
            class="mr-2" />
      </div>
    </div>
    <div class="ml-auto flex items-center space-x-2">
      <button onclick={() => modTask(index)}>
        <img src={img} alt="Modifica" width="20" height="20" />
      </button>
      <button 
          onclick={() => deleteTask(index)}
          class="text-red-500 font-bold hover:text-red-700"
      >X</button>
    </div>
  </li>
  {/each}
</ul>
</div>
