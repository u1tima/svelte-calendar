<script>
  import { days, months } from "./data.js";

  export let startOnMonday = false;
  export let selectedDate = new Date().getDate();
  export let events = null;

  const numOfCell = 42;
  const shiftDays = startOnMonday ? 1 : 0;
  const daysOfWeek = startOnMonday ? shiftDaysOfWeek(days) : days;

  let now = new Date();

  $: month = now.getMonth();
  $: year = now.getFullYear();
  $: dates = getDates(month, year);

  function shiftDaysOfWeek(days) {
    const day = days.shift();
    days.push(day);
    return days;
  }

  function getDates(month, year) {
    let result = [];

    const startDay = new Date(year, month, 1).getDay() - shiftDays;
    const daysInLastMonth = new Date(year, month, 0).getDate();
    const daysInMonth = new Date(year, month + 1, 0).getDate();

    // last month
    for (let i = daysInLastMonth - startDay + 1; i <= daysInLastMonth; i++) {
      const cls = ["date"];
      cls.push("is-hidden");

      result.push({
        date: i,
        classes: cls
      });
    }

    // this month
    for (let i = 1; i <= daysInMonth; i++) {
      const day = new Date(year, month, i).getDay();

      const cls = ["date"];
      if (day == 0 || day == 6) {
        cls.push("is-weekend");
      }

      result.push({
        date: i,
        classes: cls,
        selected: i == selectedDate
      });
    }

    // next months
    for (let i = 1; numOfCell - result.length; i++) {
      const cls = ["date"];
      cls.push("is-hidden");

      result.push({
        date: i,
        classes: cls
      });
    }
    return result;
  }

  function changeMonth(action) {
    switch (action) {
      case "up":
        month++;
        break;

      case "down":
        month--;
        break;

      default:
        break;
    }

    selectedDate = null;

    if (month > 11) {
      month = 0;
      year++;
    }

    if (month < 0) {
      month = 11;
      year--;
    }
  }

  function changeDate(e) {
    const { id } = e.target;
    if (id == "") return;
    dates = dates.map((item, index) => {
      item.selected = id == index;
      return item;
    });
  }
</script>

<style>
  .calendar {
    box-sizing: border-box;
    width: 300px;
    margin: 0 auto;
    margin-bottom: 20px;
    padding: 10px;
    border: 1px solid #ccc;
  }

  .control {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .arrow {
    padding: 10px;
    cursor: pointer;
    user-select: none;
  }

  .arrow:hover {
    color: red;
  }

  .days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    grid-template-rows: 30px;
  }

  .day {
    align-self: center;
    text-align: center;
  }

  .dates {
    display: grid;
    grid-template-columns: repeat(7, auto);
    grid-template-rows: repeat(6, 40px);
  }

  .date {
    align-self: center;
    text-align: center;
    user-select: none;
  }

  .is-hidden {
    color: #ccc;
  }

  .is-weekend {
    color: red;
  }

  .highlight {
    display: inline-block;
    width: 25px;
    height: 25px;
    border-radius: 50%;
    cursor: pointer;
  }

  .highlight:hover {
    color: #fff;
    background-color: red;
  }

  .is-selected,
  .is-selected:hover {
    color: #fff;
    background-color: blue;
  }
</style>

<div class="calendar">

  <div class="control">
    <div class="arrow" on:click={() => changeMonth('down')}>&#60</div>
    <div>{months[month]} {year}</div>
    <div class="arrow" on:click={() => changeMonth('up')}>&#62</div>
  </div>

  <div class="days">
    {#each daysOfWeek as day}
      <div class="day">{day}</div>
    {/each}
  </div>

  <div class="dates" on:click={e => changeDate(e)}>
    {#each dates as date, index}
      <div class={date.classes.join(' ')}>

        <span id={index} class="highlight {date.selected ? 'is-selected' : ''}">
          {date.date}
        </span>

      </div>
    {/each}
  </div>

</div>
