# Carbon

#### 简介

PHP时间处理的利器

#### 用法

```php
use Carbon\Carbon;

// get instance
$carbon = Carbon::now();
$carbon = Carbon::today();
$carbon = Carbon::tomorrow();
$carbon = Carbon::yesterday();

//$carbon = Carbon::now();
//$start_ts = $carbon->startOfMonth()->timestamp;
//$end_ts = $carbon->endOfMonth()->timestamp;

//$start_ts = Carbon::tomorrow()->startOfDay()->timestamp;
//$end_ts = Carbon::parse('+7 days')->endOfDay()->timestamp;

$carbon = Carbon::parse('today')
$carbon = Carbon::parse('yesterday')
$carbon = Carbon::parse('tomorrow')
$carbon = Carbon::parse('2 days ago')
$carbon = Carbon::parse('+3 days')
$carbon = Carbon::parse('+2 weeks')
$carbon = Carbon::parse('+4 months')
$carbon = Carbon::parse('-1 year')
$carbon = Carbon::parse('next wednesday')
$carbon = Carbon::parse('last friday');
$carbon = Carbon::parse('2016-10-15 00:10:25');

// powerful attributes
$carbon->dayOfWeek;
$carbon->englishDayOfWeek; // Friday
$carbon->englishMonth; // October
$carbon->dayOfYear; // 268
$carbon->weekOfYear; // 34
$carbon->daysInMonth; // 31
$carbon->timestamp; 

// to date or datetime String
$carbon->toDateString(); // Y-m-d
$carbon->toDateTimeString(); // Y-m-d H:i:s
$carbon->toTimeString(); // H:i:s
$carbon->format('Y-m-d H:i:s');

// week
$carbon = $carbon->startOfWeek();

// 日期比较

// 日期操作
$carbon->addMonth();
$carbon->subMonth();
$carbon->addWeeks();
$carbon->subWeek();
$carbon->addHour();
$carbon->addMinute();
$carbon->addSecond();
$carbon->addDay();    
$carbon->addDays(29);
$carbon->subDay();

// diffForHumans
\Illuminate\Support\Carbon::setLocale('zh');
$carbon->subDays(24)->diffForHumans(); // 3周前

// constants
Carbon::SUNDAY; // 0
Carbon::HOURS_PER_DAY; // int(24)
Carbon::MINUTES_PER_HOUR; // int(60)
Carbon::SECONDS_PER_MINUTE; // int(60)

```

