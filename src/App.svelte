<script>
  import Calendar from "./Calendar.svelte";
  
  const cb = date => {
    if (date.getDate() == 7) return "birthday";
  };
</script>

<div class="container">
  <Calendar selectedDate={new Date(2020,3,22)} />

  <Calendar />
  <Calendar startOnMonday={true} events={cb} />
</div>
