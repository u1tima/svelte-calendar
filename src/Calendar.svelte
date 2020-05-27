<script>
  import { daysMon, daysSan, months } from "./data.js";

  export let startOnMonday = false;
  export let selectedDate = 1;
  export let events;
  console.log(events);

  const numOfCell = 42;
  const shiftDays = startOnMonday ? 1 : 0;
  const days = startOnMonday ? daysMon : daysSan;

  let now = new Date();

  $: month = now.getMonth();
  $: year = now.getFullYear();
  $: dates = getDates(month, year);

  function getDates(month, year) {
    let result = [];

    const startDayOfMonth = new Date(year, month, 1).getDay() - shiftDays;
    let daysInLastMonth = new Date(year, month, 0).getDate();
    const daysInMonth = new Date(year, month + 1, 0).getDate();

    // last month
    for (let i = 0; i < startDayOfMonth; i++) {
      result.push({
        date: daysInLastMonth,
        hidden: true
      });
      daysInLastMonth--;
    }
    result = result.reverse();

    // this month
    for (let i = 1; i <= daysInMonth; i++) {
      const day = new Date(year, month, i).getDay();
      result.push({
        date: i,
        hidden: false,
        selected: i == selectedDate,
        isWeekEnd: day == 0 || day == 6
      });
    }

    // next months
    for (let i = 1; numOfCell - result.length; i++) {
      result.push({
        date: i,
        hidden: true
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
    width: 350px;
    margin: 0 auto;
    padding: 20px;
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
    grid-template-columns: repeat(7, 50px);
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
    color: #1ca1c1;
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
    {#each days as day}
      <div class="day">{day}</div>
    {/each}
  </div>

  <div class="dates" on:click={e => changeDate(e)}>
    {#each dates as date, index}
      <div
        class="date {date.hidden ? 'is-hidden' : ''}
        {date.isWeekEnd ? 'is-weekend' : ''}">

        <span id={index} class="highlight {date.selected ? 'is-selected' : ''}">
          {date.date}
        </span>
        
      </div>
    {/each}
  </div>

</div>
