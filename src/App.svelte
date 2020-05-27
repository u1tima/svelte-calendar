<script>
  import Calendar from "./Calendar.svelte";
  const cb = date => {
    if (date.getDay() == 7) return "birthday";
  };
</script>

<div class="container">
  <Calendar selectedDate={14} />
  <Calendar startOnMonday={true} events={cb} />
</div>
