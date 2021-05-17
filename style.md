ຄູ່ມືໃນການພັດທະນາພາສາ Go (Go Style Guide)

# ສາລະບານ (Table of Content)

- [ສາລະບານ (Table of Content)](#ສາລະບານ-table-of-content)
- [ບົດນຳ (Introduction)](#ບົດນຳ-introduction)
- [ແນວທາງ (Guidelines)](#ແນວທາງ-guidelines)
  - [Pointers to Interfaces](#pointers-to-interfaces)
  - [Verify Interface Compliance](#verify-interface-compliance)
  - [Receivers and Interfaces](#receivers-and-interfaces)
  - [Zero-value Mutexes are Valid](#zero-value-mutexes-are-valid)
  - [Copy Slices and Maps at Boundaries](#copy-slices-and-maps-at-boundaries)
    - [Receiving Slices and Maps](#receiving-slices-and-maps)
    - [Returning Slices and Maps](#returning-slices-and-maps)
  - [Defer to Clean Up](#defer-to-clean-up)
  - [Channel Size is One or None](#channel-size-is-one-or-none)
  - [Start Enums at One](#start-enums-at-one)
  - [Use `time` to handle time](#use-time-to-handle-time)
    - [Use `time.Time` for instants of time](#use-timetime-for-instants-of-time)
    - [Use `time.Duration` for periods of time](#use-timeduration-for-periods-of-time)
    - [Use `time.Time` and `time.Duration` with external systems](#use-timetime-and-timeduration-with-external-systems)
  - [Error Types](#error-types)
  - [Error Wrapping](#error-wrapping)
  - [Handle Type Assertion Failures](#handle-type-assertion-failures)
  - [Don't Panic](#dont-panic)
  - [Use go.uber.org/atomic](#use-gouberorgatomic)
  - [Avoid Mutable Globals](#avoid-mutable-globals)
  - [Avoid Embedding Types in Public Structs](#avoid-embedding-types-in-public-structs)
  - [Avoid `init()`](#avoid-init)
  - [Exit in Main](#exit-in-main)
    - [Exit Once](#exit-once)
- [ປະສິດທິິພາບ (Performance)](#ປະສິດທິິພາບ-performance)
  - [Prefer strconv over fmt](#prefer-strconv-over-fmt)
  - [Avoid string-to-byte conversion](#avoid-string-to-byte-conversion)
  - [Prefer Specifying Container Capacity](#prefer-specifying-container-capacity)
    - [Specifying Map Capacity Hints](#specifying-map-capacity-hints)
    - [Specifying Slice Capacity](#specifying-slice-capacity)
- [ຮູບແບບ (Style)](#ຮູບແບບ-style)
  - [Be Consistent](#be-consistent)
  - [Group Similar Declarations](#group-similar-declarations)
  - [Import Group Ordering](#import-group-ordering)
  - [Package Names](#package-names)
  - [Function Names](#function-names)
  - [Import Aliasing](#import-aliasing)
  - [Function Grouping and Ordering](#function-grouping-and-ordering)
  - [Reduce Nesting](#reduce-nesting)
  - [Unnecessary Else](#unnecessary-else)
  - [Top-level Variable Declarations](#top-level-variable-declarations)
  - [Prefix Unexported Globals with \_](#prefix-unexported-globals-with-_)
  - [Embedding in Structs](#embedding-in-structs)
  - [Local Variable Declarations](#local-variable-declarations)
  - [nil is a valid slice](#nil-is-a-valid-slice)
  - [Reduce Scope of Variables](#reduce-scope-of-variables)
  - [Avoid Naked Parameters](#avoid-naked-parameters)
  - [Use Raw String Literals to Avoid Escaping](#use-raw-string-literals-to-avoid-escaping)
  - [Initializing Structs](#initializing-structs)
    - [Use Field Names to Initialize Structs](#use-field-names-to-initialize-structs)
    - [Omit Zero Value Fields in Structs](#omit-zero-value-fields-in-structs)
  - [Initializing Maps](#initializing-maps)
  - [Format Strings outside Printf](#format-strings-outside-printf)
  - [Naming Printf-style Functions](#naming-printf-style-functions)
- [Patterns](#patterns)
  - [Test Tables](#test-tables)
  - [Functional Options](#functional-options)
- [Linting](#linting)
  - [Lint Runners](#lint-runners)

# ບົດນຳ (Introduction)

`ຮູບແບບ` ເປັນຂໍ້ຕົກລົງທີ່ຊ່ວຍຈັດລະບຽບໂຄ໋ດຂອງເຮົາ ແຕ່ຄຳວ່າ `ຮູບແບບ` ອາດຈະເຮັດໃຫ້ສັບສົນເລັກນ້ອຍ ເພາະຂໍ້ຕົກລົງນີ້ມັນຄອບຄຸມໄປຫຼາຍກວ່າເລື່ອງໄຟລຊອສໂຄ໋ດ ເພາະຖ້າເປັນແບບນັ້ນ gofmt ກໍຈັດການໃຫ້ເຮົາຢູ່ແລ້ວ.

ເປົ້າໝາຍຂອງຄູ່ມືນີ້ ຄືການຫຼຸດຄວາມຊັບຊ້ອນດ້ວຍການອະທິບາຍວ່າທີ່ Uber ເຮົາເຮັດຫຼືບໍ່ເຮັດອັນໃດຕອນທີ່ເຮົາຂຽນ Go ກັນແນ່ ແລະກົດນີ້ມີໄວ້ເພື່ອຊ່ວຍໃຫ້ຊອສໂຄ໋ດມັນເບິ່ງແຍງຈັດການງ່າຍ ໃນຂະນະທີ່ກໍຍອມໃຫ້ວິສະວະກອນຊອຟແວຣໃຊ້ມັນໄດ້ຢ່າງມີປະສິດທິພາຍດ້ວຍ.

ຄູ່ມືສະບັບນີ້ເດີມຖືກຂຽນຂຶ້ນໂດຍ [Prashant Varanasi] ແລະ [Simon Newton] ເພື່ອຊ່ວຍໃຫ້ເພື່ອນຮ່ວມງານເລີ່ມຕົ້ນຂຽນ Go ກັນໄດ້ໄວຂຶ້ນ ແຕ່ຫຼັງຈາກຜ່ານໄປຫຼາຍປີມັນກໍຖືກແກ້ໄຂເພີ່ມເຕີມຈາກຂໍ້ສະເໜີແນະຕ່າງໆທີ່ໄດ້ຮັບ.

[prashant varanasi]: https://github.com/prashantv
[simon newton]: https://github.com/nomis52

ສຳນວນການຂຽນ Go ໃນເອກະສານນີ້ເປັນແບບສະບັບທີ່ໃຊ້ກັນທີ່ Uber ຊຶ່ງປົກກະຕິກໍເປັນແນວທາງດຽວກັບການຂຽນ Go ທົ່ວໄປຢູ່ແລ້ວ ຊຶ່ງຖ້າຈະມິເພີ່ມເຕີມຈາກພາຍນອກກໍມາຈາກທີ່ເຫຼົ່ານີ້:

1. [Effective Go](https://golang.org/doc/effective_go.html)
2. [Go Common Mistakes](https://github.com/golang/go/wiki/CommonMistakes)
3. [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)

ໂຄ໋ດທັງໝົດຄວນຈະຕ້ອງບໍ່ມີ error ໃດໆຈາກ golint ແລະ govet ເຮົາແນະນຳໃຫ້ເຈົ້າຕັ້ງຄ່າໃນ editor ຕາມນີ້:

- Run `goimports` on save
- Run `golint` and `go vet` to check for errors

ເຈົ້າສາມາດຫາຂໍ້ມູນເພີ່ມເຕີມກ່ຽວກັບເຄື່ອງມືຊ່ວຍໃນ editors ໄດ້ຈາກທີ່ນີ້: <https://github.com/golang/go/wiki/IDEsAndTextEditorPlugins>

# ແນວທາງ (Guidelines)

## Pointers to Interfaces

ເຈົ້າເກືອບບໍ່ຈຳເປັນຕ້ອງໃຊ້ພ້ອຍເຕີ້ເພື່ອຊີ້ໄປ `interface` ເຈົ້າພຽງແຕ່ສົ່ງຄ່າຕົງໆເຂົ້າໄປ ແຕ່ຂໍ້ມູນເບື້ອງຫຼັງຍັງສາມາດສົ່ງເປັນພ້ອຍເຕີ້ກໍໄດ້ຄືກັນ.

interface ປະກອບໄປດ້ວຍສອງຢ່າງ:

1. ພ້ອຍເຕີ້ ຊີ້ໄປທີ່ type ຂອງສິ່ງທີ່ເກັບ ເຈົ້າຈະຄິດວ່າມັນເປັນ "type" ເລີຍກໍໄດ້.
2. ພ້ອຍເຕີ້ ຂອງສິ່ງທີ່ເກັບ ຖ້າສິ່ງນັ້ນເກັບເປັນພ້ອຍເຕີ້ ກໍຈະເກັບຕົງໆ. ແຕ່ຖ້າມັນເປັນຄ່າໃດໆກໍຕາມມັນຈະເກັບເປັນພ້ອຍເຕີ້ຂອງຄ່ານັ້ນແທນ.

ຖ້າເຈົ້າຕ້ອງການໃຫ້ເມັດທອດແກ້ໄຂຄ່າໃນຕົວມັນເອງໄດ້ ນັ້ນເຈົ້າຈຶ່ງຈະຕ້ອງໃຊ້ພ້ອຍເຕິ້.

## Verify Interface Compliance

ກວດສອບຄວາມສອດຄ້ອງຂອງ interface ໃນເວລາຄອມໄພລຕາມຄວາມເໝາະສົມ. ລວມມີ:

- ປະເພດທີ່ສົ່ງອອກທີ່ຈຳເປັນໃນການໃຊ້ງານ interface ສະເພາະ ທີ່ເປັນສ່ວນໜຶ່ງຂອງສັນຍາ API.
- ປະເພດທີ່ສົ່ງອອກ ຫຼື ບໍ່ໄດ້ສົ່ງອອກ ທີ່ເປັນສ່ວນໜື່ງຂອງກຸ່ມປະເພດທີ່ໃຊ້ງານ interface ດຽວກັນ.
- ກໍລະນີອື່ນໆທີ່ເປັນການລະເມີດ interface ຈະເຮັດໃຫ້ຜູ້ໃຊ້ເສຍຫາຍ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Handler struct {
  // ...
}

func (h *Handler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
  ...
}
```

</td><td>

```go
type Handler struct {
  // ...
}

var _ http.Handler = (*Handler)(nil)

func (h *Handler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
  // ...
}
```

</td></tr>
</tbody></table>

ຄຳສັ່ງ `var _ http.Handler = (*Handler)(nil)` ຈະລົ້ມເຫຼວຕອນຄອມໄພລ ຖ້າ `*Handler` ບໍ່ສອດຄ້ອງກັບ `http.Handler` interface.

ເບື້ອງຂວາມືຂອງການກຳນົດຄ່າຄວນເປັນຄ່າສູນຂອງປະເພດທີ່ກຳນົດ. ກໍລະນີນີ້ຄື `nil` ສຳລັບປະເພດຂອງພ້ອຍເຕີ້ (ເຊັ່ນ `*Handler`), slice, map ແລະ empty struct ສຳລັບປະເພດ struct.

```go
type LogHandler struct {
  h   http.Handler
  log *zap.Logger
}

var _ http.Handler = LogHandler{}

func (h LogHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
  // ...
}
```

## Receivers and Interfaces

ເມັດທອດທີ່ມີຕົວຮັບເປັນຄ່າປົກກະຕິສາມາດໃຊ້ເອີ້ນໃຊ້ເທິງຕົວປ່ຽນພ້ອຍເຕີ້ໄດ້ເລີຍ ລວມທັງຕົວມັນເອງ. ເມັດທອດທີ່ມີຕົວຮັບເປັນພ້ອຍເຕີ້ສາມາເດເອີ້ນໄດ້ແຕ່ເທີງຕົວປ່ຽນພ້ອຍເຕີ້ເທົ່ານັ້ນ ຫຼື [addressable values]

[addressable values]: https://golang.org/ref/spec#Method_values

ຕົວຢ່າງ:

```go
type S struct {
  data string
}

func (s S) Read() string {
  return s.data
}

func (s *S) Write(str string) {
  s.data = str
}

sVals := map[int]S{1: {"A"}}

// ເຈົ້າສາມາດເອີ້ນໃຊ້ Read ໄດ້ຢ່າງດຽວ
sVals[1].Read()

// This will not compile:
//  sVals[1].Write("test")

sPtrs := map[int]*S{1: {"A"}}

// ເຈົ້າສາມາດເອີ້ນໃຊ້ໄດ້ທັງ Read ແລະ Write ຜ່ານພ້ອຍເຕີ້
sPtrs[1].Read()
sPtrs[1].Write("test")
```

ໃນທາງກັບກັນ interface ຍອມໃຫ້ເຈົ້າແທນທີ່ດ້ວຍພ້ອຍເຕີ້ໄດ້ ເຖິງວ່າເມັດທອດຈະໃຊ້ຕົວຮັບເປັນຄ່າປົກກະຕິ.

```go
type F interface {
  f()
}

type S1 struct{}

func (s S1) f() {}

type S2 struct{}

func (s *S2) f() {}

s1Val := S1{}
s1Ptr := &S1{}
s2Val := S2{}
s2Ptr := &S2{}

var i F
i = s1Val
i = s1Ptr
i = s2Ptr

// ໂຄ໋ດດ້ານລຸ່ມນີ້ບໍ່ສາມາດເຮັດວຽກໄດ້ ເນື່ອງຈາກ s2Val ເປັນຄ່າປົກກະຕິ ໃນຂະນະທີ່ຕົວຮັບໃນເມັດທອດບໍ່ໄດ້ເປັນຄ່າປົກກະຕິແຕ່ເປັນພ້ອຍເຕີ້.
// i = s2Val
```

Effective Go ຂຽນອະທິບາຍເລື່ອງນີ້ໄວ້ດີຫຼາຍໃນເລື່ອງ [Pointers vs. Values].

[pointers vs. values]: https://golang.org/doc/effective_go.html#pointers_vs_values

## Zero-value Mutexes are Valid

ຄ່າ zero-value ຂອງ `sync.Mutex` ແລະ `sync.RWMutex` ສາມາດໃຊ້ງານໄດ້ໂດຍບໍ່ຕ້ອງ initial ນັ້ນແປວ່າເຈົ້າເກືອບບໍ່ຈຳເປັນຕ້ອງໃຊ້ພ້ອຍເຕີ້ເລິຍ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
mu := new(sync.Mutex)
mu.Lock()
```

</td><td>

```go
var mu sync.Mutex
mu.Lock()
```

</td></tr>
</tbody></table>

ຖ້າເຈົ້າໃຊ້ struct ດ້ວຍພ້ອຍເຕີ້ mutex ສາມາດເປັນຟິວທີ່ບໍ່ມີພ້ອຍເຕີ້ໄດ້.

struct ທີ່ບໍ່ໄດ້ເປີດເຜີຍສູ່ພາຍນອກທີ່ໃຊ້ mutex ປົກປ້ອງຟິວໃນຕົວມັນເອງ ອາດຈະຝັງ mutex ໄວ້ແບບນີ້

<table>
<tbody>
<tr><td>

```go
type smap struct {
  sync.Mutex // ໃຊ້ສະເພາະ type ທີ່ບໍ່ເປີດເຜີຍສູ່ພາຍນອກ

  data map[string]string
}

func newSMap() *smap {
  return &smap{
    data: make(map[string]string),
  }
}

func (m *smap) Get(k string) string {
  m.Lock()
  defer m.Unlock()

  return m.data[k]
}
```

</td><td>

```go
type SMap struct {
  mu sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.mu.Lock()
  defer m.mu.Unlock()

  return m.data[k]
}
```

</td></tr>

</tr>
<tr>
<td>ການຝັງໃຊ້ກັບ type ທີ່ຢູ່ພາຍໃນ ຫຼື type ທີ່ຕ້ອງການເຮັດຕົວເອງເປັນ Mutex interface</td>

<td>ສຳລັບ type ທີ່ຕ້ອງການເປີດເຜີຍສູ່ພາຍນອກໃຫ້ໃຊ້ແບບຟິວພາຍໃນ struct</td>
</tr>

</tbody></table>

## Copy Slices and Maps at Boundaries

slices ແລະ maps ເກັບຄ່າເປັນພ້ອຍເຕີ້ ດັ່ງນັ້ນໃຫ້ລະມັດລະວັງເວລາທີ່ຈະ copy ຄ່າເຫຼົ່ານີ້.

### Receiving Slices and Maps

ຕ້ອງຈື່ໄວ້ວ່າ maps ຫຼຶ slices ທີ່ເຈົ້າຮັບເຂົ້າມາເປັນອາກິວເມັນ ກໍຖືກຄົນທີ່ໃຊ້ມັນແກ້ໄຂໄດ້ ຖ້າເຈົ້າເກັບຂໍ້ມູນຊະນິດທີ່ມັນອ້າງເຖິງກັນ.

<table>
<thead><tr><th>Bad</th> <th>Good</th></tr></thead>
<tbody>
<tr>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = trips
}

trips := ...
d1.SetTrips(trips)

// ເຈົ້າຕ້ອງການຈະແກ້ໄຂຄ່າ d1.trips ຫຼືບໍ່?
trips[0] = ...
```

</td>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = make([]Trip, len(trips))
  copy(d.trips, trips)
}

trips := ...
d1.SetTrips(trips)

// ຕອນນີ້ເຮົາກໍສາມາດແກ້ໄຂ trips[0] ໂດຍບໍ່ກະທົບ d1.trips ໄດແລ້ວ.
trips[0] = ...
```

</td>
</tr>

</tbody>
</table>

### Returning Slices and Maps

ໃນທາງກັບກັນ ໃຫ້ລະມັດລະວັງການແກ້ໄຂຄ່າໄປທີ່ maps ຫຼື slices ທີ່ເປີດເຜີຍສູ່ພາຍນອກໃນລະດັບພາຍໃນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

// Snapshot ຄືນຄ່ານະເວລາປັດຈຸບັນ.
func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  return s.counters
}

// snapshot ບໍ່ໄດ້ຖືກປ້ອງກັນໂດຍ mutex ດັ່ງນັ້ນໃຜກໍຕາມທີ່ສາມາດເຂົ້າເຖິງ snapshot ສາມາດເກິດການແຂ່ງຂັນກັນໄດ້ (data races)
snapshot := stats.Snapshot()
```

</td><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  result := make(map[string]int, len(s.counters))
  for k, v := range s.counters {
    result[k] = v
  }
  return result
}

// Snapshot is now a copy.
snapshot := stats.Snapshot()
```

</td></tr>
</tbody></table>

## Defer to Clean Up

ໃຊ້ defer ເພື່ອຄືນຊັບພະຍາກອນທີ່ຈອງ ຫຼື ນຳໄປໃຊ້ງານຕ່າງໆເຊັ່ນ: ໄຟລ ແລະ ສິ່ງທີ່ຖືກລ໋ອກໄວ້.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
p.Lock()
if p.count < 10 {
  p.Unlock()
  return p.count
}

p.count++
newCount := p.count
p.Unlock()

return newCount

// ມັນງ່າຍທີ່ຈະລືມປົດລ໋ອກ ເວລາທີ່ມີການຄືນຄ່າຫຼາຍບ່ອນ
```

</td><td>

```go
p.Lock()
defer p.Unlock()

if p.count < 10 {
  return p.count
}

p.count++
return p.count

// more readable
```

</td></tr>
</tbody></table>

Defer ໃຊ້ເວລາເຮັດວຽກນ້ອຍຫຼາຍ ຖ້າຈະບໍ່ໃຊ້ມັນກໍຕໍ່ເມື່ອເຈົ້າໝັ້ນໃຈແລ້ວວ່າຟັງຊັ່ນຂອງເຈົ້າຈະເຮັດວຽກໄວຂຶຶ້ນໃນລະດັບ nanoseconds ຖ້າເຈົ້າໃຊ້ defer ມັນອ່ານງ່າຍແນ່ນອນ ແລະຄຸ້ມຄ່າທີ່ຈະໃຊ້ ໂດຍສະເພາະຢ່າງຍິ່ງເມື່ອເຈົ້າມີເມັດທອດຂະໜາດໃຫຍ່ທີ່ມີການໃຊ້ໜ່ວຍຄວາມຈຳແບບທ່າຍາກ ແລະມີການຄຳນວນຢ່າງອື່ນທີ່ສຳຄັນກວ່າການໃຊ້ `defer`.

## Channel Size is One or None

Channels ປົກກະຕິຄວນມີຂະໜາດຢູ່ທີ່ 1 ຫຼືບໍ່ມີບັບເຟີເລີຍ ໂດຍຄ່າຕັ້ງຕົ້ນ channels ຈະເປັນແບບບໍ່ມີບັບເຟີ ແລະມີຂະໜາດເປັນສູນ ຂະໜາດອື່ນໆຂຶ້ນຢູ່ກັບວິຈາລະນະຍານ ຂຶ້ນຢູ່ກັບວ່າຈະປ້ອງການການເຕີມເຕັມໃນຂະນະທີ່ກຳລັງໂຫຼດ ແລະມີການຂຽນແນວໃດ

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// ຫວັງວ່າຈະພໍສຳລັບທຸກຄົນ!
c := make(chan int, 64)
```

</td><td>

```go
// ໃຫ້ຂະໜາດເປັນ 1
c := make(chan int, 1) // ຫຼື
// ບໍ່ມີບັບເຟີເລີຍ, ຂະໜາດເປັນສູນ
c := make(chan int)
```

</td></tr>
</tbody></table>

## Start Enums at One

ວິທີມາດຕະຖານໃນການສ້າງ enum ໃນ go ຄືການສ້າງປະເພດຂຶ້ນມາເອງ ຫຼືປະກາດເປັນກຸ່ມ ‍`const` ດ້ວຍການໃຊ້ `iota` ຊຶ່ງປົກກະຕິຕົວປ່ຽນຈະມີຄ່າຕັ້ງຕົ້ນເປັນ 0 ສະເໝີ ເພາະສະນັ້ນເວລາທີ່ເຈົ້າຈະສ້າງ enum ຄວນຈະເລີ່ມດ້ວຍຄ່າທີ່ບໍ່ແມ່ນສູນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota
  Subtract
  Multiply
)

// Add=0, Subtract=1, Multiply=2
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

// Add=1, Subtract=2, Multiply=3
```

</td></tr>
</tbody></table>

ມັນກໍມີບາງກໍລະນີຄືກັນທີ່ການໃຊ້ສູນອາດຈະເໝາະສົມກວ່າ ຂຶ້ນຢູ່ກັບສະຖານະການ.

```go
type LogOutput int

const (
  LogToStdout LogOutput = iota
  LogToFile
  LogToRemote
)

// LogToStdout=0, LogToFile=1, LogToRemote=2
```

## Use `time` to handle time

ເວລາມີຄວາມຊັບຊ້ອນ ສົມມຸດຕິຖານທີ່ບໍ່ຖືກຕ້ອງມັກເກີດຂຶ້ນກ່ຽວກັບເວລາມີດັ່ງຕໍ່ໄປນີ້.

1. 1ມື້ ມີ 24ຊົ່ວໂມງ
2. 1ຊົ່ວໂມງ ມີ 60ນາທີ
3. 1ອາທິດ ມີ 7ມື້
4. 1ປີ ມີ 365ມື້
5. [ແລະອື່ນໆ](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time)

ຕົວຢ່າງ **1** ໜາຍເຖິງການເພີ່ມເວລາ 24ຊົ່ວໂມງ ໃນທັນທີໃຫ້ກັບຕົວປ່ຽນເວລາຈະບໍ່ເພີ່ມມື້ຕາມປະຕິທິນສະເໝີໄປ.

ດັ່ງນັ້ນຄວນໃຊ້ແພັກເກັດ [`"time"`] ສະເໝີ ເມື່ອຕ້ອງຮັບມືກັບເວລາເພາະຈະຊ່ວຍຈັດການກັບສົມມຸດຕິຖານທີ່ບໍ່ຖືກຕ້ອງເຫຼົ່ານີ້ໄດ້ຢ່າງປອດໄພ ແລະ ແມ້ນຢຳຍິ່ງຂຶ້ນ.

[`"time"`]: https://golang.org/pkg/time/

### Use `time.Time` for instants of time

ໃຊ້ [`time.Time`] ເມື່ອຈັດການກັບຕົວປ່ຽນຂອງເວລາ ແລະວິທີການກ່ຽວກັບ `time.Time` ເມື່ອປຽບທຽບ, ເພີ່ມ ຫຼືລົບເວລາ.

[`time.time`]: https://golang.org/pkg/time/#Time

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func isActive(now, start, stop int) bool {
  return start <= now && now < stop
}
```

</td><td>

```go
func isActive(now, start, stop time.Time) bool {
  return (start.Before(now) || start.Equal(now)) && now.Before(stop)
}
```

</td></tr>
</tbody></table>

### Use `time.Duration` for periods of time

ໃຊ້ [`time.Duration`] ເມື່ອຈັດການກັບຊ່ວງເວລາ.

[`time.duration`]: https://golang.org/pkg/time/#Duration

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func poll(delay int) {
  for {
    // ...
    time.Sleep(time.Duration(delay) * time.Millisecond)
  }
}

poll(10) // ເປັນ ວິນາທີ ຫຼຶ ມິນລິວິນາທີ?
```

</td><td>

```go
func poll(delay time.Duration) {
  for {
    // ...
    time.Sleep(delay)
  }
}

poll(10*time.Second)
```

</td></tr>
</tbody></table>

ກັບໄປທີ່ຕົວຢ່າງຂອງການເພີ່ມເວລາ 24ຊົ່ວໂມງ ໃນທັນທີວິທີທີ່ເຮົາໃຊ້ໃນການເພີ່ມເວລາຂຶ້ນຢູ່ກັບເຈດຕະນາ ຫາກເຮົາຕ້ອງການເວລາດຽວກັນຂອງມື້ ແຕ່ໃນມື້ຖັດໄປຕາມປະຕິທິນເຮົາຄວນໃຊ້ [`Time.AddDate`]. ຢ່າງໃດກໍຕາມ ຫາກເຮົາຕ້ອງການເວລາທີ່ຮັບປະກັນວ່າຈະເປນ 24ຊົ່ວໂມງ ຫຼັງຈາກຄັ້ງກ່ອນໜ້ານີ້ຄວນໃຊ້ [`Time.Add`]

[`time.adddate`]: https://golang.org/pkg/time/#Time.AddDate
[`time.add`]: https://golang.org/pkg/time/#Time.Add

```go
newDay := t.AddDate(0 /* ປີ */, 0 /* ເດືອນ */, 1 /* ວັນ */)
maybeNewDay := t.Add(24 * time.Hour)
```

### Use `time.Time` and `time.Duration` with external systems

ໃຊ້ `time.Duration` ແລະ `time.Time` ໃນການຕອບໂຕ້ກັບລະບົບພາຍນອກເມື່ອເປັນໄປໄດ້, ຕົວຢ່າງ:

- Command-line flags: [`flag`] ສະໜັບສະໜູນ `time.Duration` ຜ່ານ
  [`time.ParseDuration`]
- JSON: [`encoding/json`] ສະໝັບສະໝູນ encoding `time.Time` ໂດຍ [RFC 3339]
  string ຜ່ານ [`UnmarshalJSON`]
- SQL: [`database/sql`] ສະໝັບສະໝູນການແປງ `DATETIME` ຫຼຶ `TIMESTAMP` columns
  ເປັນ `time.Time` ແລະກັບກັນ ຖ້າຫາກ driver ທີ່ໃຊ້ສະໜັບສະໜູນ
- YAML: [`gopkg.in/yaml.v2`] ສະໝັບສະໝູນ `time.Time` ໂດຍ [RFC 3339] string, ແລະ
  `time.Duration` ຜ່ານ [`time.ParseDuration`]

  [`flag`]: https://golang.org/pkg/flag/
  [`time.parseduration`]: https://golang.org/pkg/time/#ParseDuration
  [`encoding/json`]: https://golang.org/pkg/encoding/json/
  [rfc 3339]: https://tools.ietf.org/html/rfc3339
  [`unmarshaljson` method]: https://golang.org/pkg/time/#Time.UnmarshalJSON
  [`database/sql`]: https://golang.org/pkg/database/sql/
  [`gopkg.in/yaml.v2`]: https://godoc.org/gopkg.in/yaml.v2

ເມື່ອມັນເປັນໄປບໍ່ໄດ້ທີ່ໃຊ້ `time.Duration` ໃນການໂຕ້ຕອບເຫຼົ່ານີ້ ໃຫ້ໃຊ້ `int` ຫຼື `float64` ແລະລວມຫົວໜ່ວຍໃນຊື່ຟິວ.

ຕົວຢ່າງ ເນື່ອງຈາກ `encoding/json` ບໍ່ຮອງຮັບ `time.Duration` ຫົວໜ່ວຍຈະລວມຢູ່ໃນຊື່ຟິວ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// {"interval": 2}
type Config struct {
  Interval int `json:"interval"`
}
```

</td><td>

```go
// {"intervalMillis": 2000}
type Config struct {
  IntervalMillis int `json:"intervalMillis"`
}
```

</td></tr>
</tbody></table>

ເມື່ອບໍ່ສາມາດໃຊ້ `time.Time` ໃນການໂຕ້ຕອບເຫຼົ່ານີ້ໄດ້ ເວັ້ນແຕ່ຈະມີການຕົກລົງທາງເລືອກອື່ນ ໃຫ້ໃຊ້ `string` ແລະຈັດຮູບແບບການປະທັບເວລາຕາມທີ່ກຳນົດ [RFC 3339] ຮູບແບບນີ້ຖືກໃຊ້ເປັນຄ່າເລີ່ມຕົ້ນໂດຍ [`Time.UnmarshalText`] ແລະພ້ອມໃຊ້ງານໃນ `Time.Format` ແລະ `time.Parse` ຜ່ານ [`time.RFC3339`].

[`time.unmarshaltext`]: https://golang.org/pkg/time/#Time.UnmarshalText
[`time.rfc3339`]: https://golang.org/pkg/time/#RFC3339

ເຖິງວ່າສິ່ງນີ້ຈະບໍ່ເປັນບັນຫາໃນທາງປະຕິບັດ ແຕ່ໃຫ້ຮູ້ວ່າແພັກເກັດ `"time"` ບໍ່ຮອງຮັບການແຍກການປະທັບເວລາດ້ວຍອະທິກະວິນາທີ ([8728]) ແລະບໍ່ມີການຄຳນວນເປັນອະທິກະວິນາທີ ([15190]). ຫາກເຈົ້າປຽບທຽບເວລາສອງຕົວຄວາມແຕກຕ່າງຈະບໍ່ລວມອະທິກະວິນາທີທີ່ອາດເກີດຂື້ນລະຫວ່າງສອງຕົວປ່ຽນເວລານັ້ນ.

[8728]: https://github.com/golang/go/issues/8728
[15190]: https://github.com/golang/go/issues/15190

## Error Types

ການສ້າງ error ເຮັດໄດ້ຫຼາຍວິທີ:

- [`errors.New`] ເມື່ອສ້າງຈາກຂໍ້ຄວາມງ່າຍໆ.
- [`fmt.Errorf`] ເມື່ອຕ້ອງການຈັດຮູບແບບຂໍ້ຄວາມ.
- ສ້າງ type ທີ່ implement `Error()` ເມັດທອດ.
- ຫຸ້ມ error ດ້ວຍການໃຊ້ [`"pkg/errors".Wrap`].

ເມື່ອຕ້ອງຄືນຄ່າ errors ທາງເລືອກໃດຈະດີທີ່ສຸດ ລອງຕັ້ງຄຳຖາມເບິ່ງວ່າ:

- ນີ້ເປັນ error ທີ່ຕ້ອງການຂໍ້ມູນເພີ່ມເຕີມເປັນພິເສດບໍ່? ຖ້າບໍ່ກໍໃຊ້ [`errors.New`] ກໍໜ້າຈະພຽງພໍ.
- ຄົນທີ່ຈະເອົາ error ນີ້ໄປໃຊ້ຕໍ່ ເຂົາຕ້ອງການຈະສືບຫາບໍ່ວ່ານີ້ເປັນ error ແບບໃດ ຖ້າແມ່ນເຈົ້າຄວນສ້າງ type ທີ່ implement ເມັດທອດ `Error()` ຂຶ້ນມາໃຊ້ເອງ.
- ເຈົ້າຕ້ອງການບອກຄົນອື່ນບໍ່ວ່ານີ້ເປັນ error ທີ່ເກີດຂຶ້ນບ່ອນໃດ ຖ້າແມ່ນໃຫ້ໃຊ້ [section on error wrapping](#error-wrapping).
- ໃນກໍລະນີອື່ນໆ [`fmt.Errorf`] ກໍເປັນຕົວເລືອກທີ່ດີ.

  [`errors.new`]: https://golang.org/pkg/errors/#New
  [`fmt.errorf`]: https://golang.org/pkg/fmt/#Errorf
  [`"pkg/errors".wrap`]: https://godoc.org/github.com/pkg/errors#Wrap

ຖ້າຜູ້ໃຊ້ຕ້ອງການສືບຫາວ່າເປັນ error ປະເພດໃດ ແລະເຈົ້າຢາກສ້າງມັນດ້ວຍ [`errors.New`] ກໍຂໍໃຫ້ເຮັດໃຫ້ມັນເປັນຕົວປ່ຽນດີກວ່າ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// package foo

func Open() error {
  return errors.New("could not open")
}

// package bar

func use() {
  if err := foo.Open(); err != nil {
    if err.Error() == "could not open" {
      // handle
    } else {
      panic("unknown error")
    }
  }
}
```

</td><td>

```go
// package foo

var ErrCouldNotOpen = errors.New("could not open")

func Open() error {
  return ErrCouldNotOpen
}

// package bar

if err := foo.Open(); err != nil {
  if errors.Is(err, foo.ErrCouldNotOpen) {
    // handle
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

ຖ້າເຈົ້າມີ error ທີ່ຜູ້ໃຊ້ຕ້ອງການສືບຫາວ່າມັນເປັນປະເພດໃດ ແລະເຈົ້າກໍຕ້ອງການເພີ່ມຂໍ້ມູນລົງໄປໃນນັ້ນ (ບໍ່ແມ່ນຄ່າຄົງທີ່) ຖ້າເຊັ່ນນັ້ນເຈົ້າກໍຕ້ອງສ້າງ type ມາໃຊ້ເອງ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func open(file string) error {
  return fmt.Errorf("file %q not found", file)
}

func use() {
  if err := open("testfile.txt"); err != nil {
    if strings.Contains(err.Error(), "not found") {
      // handle
    } else {
      panic("unknown error")
    }
  }
}
```

</td><td>

```go
type errNotFound struct {
  file string
}

func (e errNotFound) Error() string {
  return fmt.Sprintf("file %q not found", e.file)
}

func open(file string) error {
  return errNotFound{file: file}
}

func use() {
  if err := open("testfile.txt"); err != nil {
    if _, ok := err.(errNotFound); ok {
      // handle
    } else {
      panic("unknown error")
    }
  }
}
```

</td></tr>
</tbody></table>

ໃຫ້ລະມັດລະວັງການເປີດເຜີຍ error type ທີ່ເຈົ້າສ້າງມັນຂຶ້ນມາອອກສູ່ພາຍນອກໂດຍຕົງ ແນະນຳໃຫ້ເຈົ້າສ້າງຟັງຊັ່ນທີ່ໃຊ້ກວດສອບ type ຂອງ error ນີ້ອອກໄປແທນຈະດີກວ່າ.

```go
// package foo

type errNotFound struct {
  file string
}

func (e errNotFound) Error() string {
  return fmt.Sprintf("file %q not found", e.file)
}

func IsNotFoundError(err error) bool {
  _, ok := err.(errNotFound)
  return ok
}

func Open(file string) error {
  return errNotFound{file: file}
}

// package bar

if err := foo.Open("foo"); err != nil {
  if foo.IsNotFoundError(err) {
    // handle
  } else {
    panic("unknown error")
  }
}
```

## Error Wrapping

ມີສາມວິທີທີ່ຈະບອກໃຫ້ຜູ້ໃຊ້ຮູ້ວ່າການເຮັດວຽກຜິດພາດ:

- ສົ່ງ error ເກົ່າກັບອອກໄປເລີຍ ຖ້າເຈົ້າບໍ່ຕ້ອງການເພີ່ມຄຳອະທິບາຍໃດໆ ແລະຢາກໃຫ້ເຫັນ error ດິບໆແບບນັ້ນ.
- ເພີ່ມຄຳອະທິບາຍລົງໄປດ້ວຍການໃຊ້ [`"pkg/errors".Wrap`] ແລະ [`"pkg/errors".Cause`] ເວລາທີ່ຕ້ອງການຖອດເອົາ error ເດີມອອກມາ.
- ໃຊ້ [`fmt.Errorf`] ຖ້າຜູ້ໃຊ້ບໍ່ຢາກຮູ້ວ່າເປັນ error ແບບໃດຊັດເຈນ

ເຮົາແນະນຳໃຫ້ເພີ່ມຄຳອະທິບາຍລົງໄປຖ້າເຮັດໄດ້ ແທນທີ່ຈະໃຫ້ເຫັນ error ແບບຄຸມເຄືອເຊັ່ນ "connection refused" ແລະເພີ່ມຄຳອະທິບາຍໃຫ້ມີຄວາມໝາຍລົງໄປເຊັ່ນ "call service foo: connection refused".

ເວລາທີ່ເຈົ້າຈະເພີ່ມຄຳອະທິບາຍໃນ error ໃຫ້ໃຊ້ປະໂຫຍກທີ່ກະທັດຮັດ ແລ້ວບໍ່ຕ້ອງໃຊ້ຄຳເວິ່ນເວີ້ເຊັ່ນ "failed to" ຖ້າບໍ່ດັ່ງນັ້ນ ເມື່ອຜ່ານໄປຫຼາຍຊັ້ນມັນຈະກາຍເປັນຂຶ້ນເຫຍື້ອ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "failed to create new store: %v", err)
}
```

</td><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "new store: %v", err)
}
```

<tr><td>

```
failed to x: failed to y: failed to create new store: the error
```

</td><td>

```
x: y: new store: the error
```

</td></tr>
</tbody></table>

ຢ່າງໃດກໍຕາມ ເວລາທີ່ error ຖືກສົ່ງໄປທີ່ລະບົບອື່ນ ມັນຄວນມີຄວາມຊັດເຈນໃນຂໍ້ຄວາມ (ຕົວຢ່າງ ຕິດປ້າຍວ່າ `err` ຫຼື ໃຊ້ຄຳນຳນ້າ "Failed" ຕອນຂຽນ logs)

See also [Don't just check errors, handle them gracefully].

[`"pkg/errors".cause`]: https://godoc.org/github.com/pkg/errors#Cause
[don't just check errors, handle them gracefully]: https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully

## Handle Type Assertion Failures

ການຮັບຄ່າດຽວຕອນທີ່ເຮັດ [type assertion] ມັດອາດຈະ panic ຖ້າມັນບໍ່ຖືກ ດັ່ງນັ້ນໃຫ້ໃຊ້ສຳນວນແບບ "comma ok" ສະເໝີ.

[type assertion]: https://golang.org/ref/spec#Type_assertions

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
t := i.(string)
```

</td><td>

```go
t, ok := i.(string)
if !ok {
  // handle the error gracefully
}
```

</td></tr>
</tbody></table>

## Don't Panic

ໂຄ໋ດທີ່ຈະຂຶ້ນ Production ຢ່າໃຊ້ panics ເພາະ Panic ເປັນຕົວຫຼັກຂອງການເກີດ [cascading failures] ຖ້າມັນເກີດ error ຂຶ້ນ ກໍໃຫ້ຟັງຊັ່ນຄືນ error ອອກໄປໃຫ້ຄົນທີ່ເອີ້ນໃຊ້ໄປຕັດສິນໃຈຈັດການເອົາເອງ.

[cascading failures]: https://en.wikipedia.org/wiki/Cascading_failure

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func run(args []string) {
  if len(args) == 0 {
    panic("an argument is required")
  }
  // ...
}

func main() {
  run(os.Args[1:])
}
```

</td><td>

```go
func run(args []string) error {
  if len(args) == 0 {
    return errors.New("an argument is required")
  }
  // ...
  return nil
}

func main() {
  if err := run(os.Args[1:]); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

</td></tr>
</tbody></table>

panic/recover ບໍ່ແມ່ນວິທີຈັດການ error ເພາະໂປຣແກຣມຈະ panic ສະເພາະເມື່ອເກີດເຫດການທີ່ຄາດບໍ່ເຖິງເຊັ່ນ ໄປອ້າງອິງເຖິງຫຍັງກໍແລ້ວແຕ່ ກັບຄ່າ nil ເວັ້ນແຕ່ຈະເປັນຊ່ວງກຽມການກ່ອນໂປຣແກຣມຈະເລີ່ມເຮັດວຽກ ຖ້າເກີດເຫດບໍ່ຄາດຄິດກໍຄວນຈະຫຍຸດການເຮັດວຽກຂອງໂປຣແກຣມໄປເລີຍ.

```go
var _statusTemplate = template.Must(template.New("name").Parse("_statusHTML"))
```

ເຖິງແມ່ນວ່າໃນ tests ກໍແນະນຳໃຫ້ໃຊ້ `t.Faltal` ຫຼື `t.FailNow` ຫຼາຍກວ່າການເຮັດໃຫ້ມັນ panic ເພື່ອບອກໃຫ້ເທສຮູ້ວ່າເກີດຂໍ້ຜິດພາດ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestFoo(t *testing.T)

f, err := ioutil.TempFile("", "test")
if err != nil {
  panic("failed to set up test")
}
```

</td><td>

```go
// func TestFoo(t *testing.T)

f, err := ioutil.TempFile("", "test")
if err != nil {
  t.Fatal("failed to set up test")
}
```

</td></tr>
</tbody></table>

## Use go.uber.org/atomic

atomic ໃນແພັກເກັດ [sync/atomic] ໃຊ້ໄດ້ກັບ type ດິບໆ (`int32`, `int64`, ແລະອື່ນໆ) ເຮົາເລີຍລືມທີ່ຈະໃຊ້ມັນເວລາຈະອ່ານ ຫຼືແກ້ໄຂຄ່າຕົວປ່ຽນ.

[go.uber.org/atomic] ໄດ້ເພີ່ມ type ທີ່ປອດໄພເຂົ້າໄປອີກ ໂດຍເຊື່ອງ type ແທ້ໆໄວ້ດ້ານລຸ່ມ ນອກຈາກນີ້ຍັງເພີ່ມ type `atomic.Bool` ເພື່ອໃຫ້ສະດວກຂຶ້ນຕື່ມ.

[sync/atomic]: https://golang.org/pkg/sync/atomic
[go.uber.org/atomic]: https://godoc.org/go.uber.org/atomic

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type foo struct {
  running int32  // atomic
}

func (f* foo) start() {
  if atomic.SwapInt32(&f.running, 1) == 1 {
     // already running…
     return
  }
  // start the Foo
}

func (f *foo) isRunning() bool {
  return f.running == 1  // race!
}
```

</td><td>

```go
type foo struct {
  running atomic.Bool
}

func (f *foo) start() {
  if f.running.Swap(true) {
     // already running…
     return
  }
  // start the Foo
}

func (f *foo) isRunning() bool {
  return f.running.Load()
}
```

</td></tr>
</tbody></table>

## Avoid Mutable Globals

ຫຼີກລ້ຽງການປ່ຽນແປງຕົວປ່ຽນລະດັບເທິງ `global` ໃຫ້ປ່ຽນໄປໃຊ້ວິທີສີດຄ່າເຂົ້າໄປແທນ `injection`. ສິ່ງນີ້ໃຊ້ກັບຟັງຊັ່ນພ້ອຍເຕີ້ເຊັ່ນດຽວກັນກັບຄ່າປະເພດອື່ນໆ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// sign.go

var _timeNow = time.Now

func sign(msg string) string {
  now := _timeNow()
  return signWithTime(msg, now)
}
```

</td><td>

```go
// sign.go

type signer struct {
  now func() time.Time
}

func newSigner() *signer {
  return &signer{
    now: time.Now,
  }
}

func (s *signer) Sign(msg string) string {
  now := s.now()
  return signWithTime(msg, now)
}
```

</td></tr>
<tr><td>

```go
// sign_test.go

func TestSign(t *testing.T) {
  oldTimeNow := _timeNow
  _timeNow = func() time.Time {
    return someFixedTime
  }
  defer func() { _timeNow = oldTimeNow }()

  assert.Equal(t, want, sign(give))
}
```

</td><td>

```go
// sign_test.go

func TestSigner(t *testing.T) {
  s := newSigner()
  s.now = func() time.Time {
    return someFixedTime
  }

  assert.Equal(t, want, s.Sign(give))
}
```

</td></tr>
</tbody></table>

## Avoid Embedding Types in Public Structs

ປະເພດການຝັງຕົວເຫຼົ່ານີ້ມີການຮົ່ວໄຫຼລາຍລະອຽດຂອງການປະຕິບັດ, ຢັບຢັ້ງການພັດທະນາ ແລະ ເອກະສານທີ່ຄຸມເຄືອ.

ສົມມຸດວ່າເຈົ້າໄດ້ປະຕິບັດປະເພດ list ທີ່ຫຼາກຫຼາຍ ໂດຍໃຊ້ `AbstractList` ຮ່ວມກັນ, ໃຫ້ເຈົ້າຂຽນສະເພາະເມັດທອດທີ່ປະເພດ list ຮູບປະທຳ (concrete list) ຂອງເຈົ້າໃຊ້ກະທຳກັບປະເພດ list ນາມມະທຳ (abstract list), ແທນທີ່ຈະຝັງ `AbstractList` ລົງໄປໃນລາຍການທີ່ເປັນຮູບປະທຳຂອງເຈົ້າ.

```go
type AbstractList struct {}

// Add adds an entity to the list.
func (l *AbstractList) Add(e Entity) {
  // ...
}

// Remove removes an entity from the list.
func (l *AbstractList) Remove(e Entity) {
  // ...
}
```

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// ConcreteList is a list of entities.
type ConcreteList struct {
  *AbstractList
}
```

</td><td>

```go
// ConcreteList is a list of entities.
type ConcreteList struct {
  list *AbstractList
}

// Add adds an entity to the list.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove removes an entity from the list.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Go ອະນຸຍາດໃຫ້ [type embedding] ເປັນການປະນີປະນອມລະຫວ່າງການສືບທອດ (inheritance) ແລະການປະກອບ (composition). ປະເພດພາຍນອກຈະໄດ້ຮັບສຳເນົາເມັດທອດຂອງປະເພດຝັງໂດຍອັດຕະໂນມັດ. ໂດຍຄ່າເລີ່ມຕົ້ນຂອງເມັດທອດເຫຼົ່ານີ້ແມ່ນຄືກັນກັບປະເພດຂອງຕົວປ່ຽນທີ່ຝັງໄວ້.

[type embedding]: https://golang.org/doc/effective_go.html#embedding

struct ຍັງໄດ້ຮັບຟິວຂໍ້ມູນໂດຍໃຊ້ຊື່ດຽວກັນກັບປະເພດ, ດັ່ງນັ້ນຫາກປະເພດທີ່ຝັງໄວ້ເປັນສາທາລະນະ ຟິວຈະເປັນແບບສາທາລະນະ ເພື່ອຮັກສາຄວາມເຂົ້າກັນໄດ້(backward compatibility) ປະເພດພາຍນອກທຸກເວີຊັ່ນໃນອານາຄົດຈະຕ້ອງເກັບປະເພດທີ່ຝັງໄວ້.

ປະເພດການຝັງຕົວເກືອບບໍ່ຈຳເປັນ ມັນເປັນພຽງຄວາມສະດວກສະບາຍທີ່ຊ່ວຍໃຫ້ເຈົ້າຫຼີກລ້ຽງການຂຽນເມັດທອດທີ່ໜ້າເບື່ອ.

ແມ້ແຕ່ການຝັງ AbstractList _interface_ ທີ່ສອດຄ້ອງກັນ ແທນທີ່ຈະເປັນ struct ຈະຊ່ວຍໃຫ້ນັກພັດທະນາມີຄວາມຫຍືດຫຍຸ່ນຫຼາຍຂຶ້ນໃນການປ່ຽນແປງໃນອານາຄົດ ແຕ່ກໍຍັງຮົ່ວໄຫຼໃນລາຍລະອຽດທີ່ປະເພດ list ຮູບປະທຳໃຊ້ງານການປະຕິບັດຂອງນາມມະທຳ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// AbstractList is a generalized implementation
// for various kinds of lists of entities.
type AbstractList interface {
  Add(Entity)
  Remove(Entity)
}

// ConcreteList is a list of entities.
type ConcreteList struct {
  AbstractList
}
```

</td><td>

```go
// ConcreteList is a list of entities.
type ConcreteList struct {
  list AbstractList
}

// Add adds an entity to the list.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove removes an entity from the list.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

ບໍ່ວ່າຈະເປັນ struct ຫຼື interface ທີ່ຝັງຢູ່ ປະເພດທີ່ຝັງໄວ້ຈະຈຳກັດການພັດທະນາຂອງປະເພດ.

- ການເພີ່ມເມັດທອດລົງໄປໃນ interfacce ທີ່ຝັງໄວ້ແມ່ນການປ່ຽນແປງຄັ້ງໃຫຍ່ (breaking change).
- ການລົບເມັດທອດອອກຈາກ struct ທີ່ຝັງໄວ້ແມ່ນການປ່ຽນແປງຄັ້ງໃຫຍ່ (breaking change).
- ການລົບປະເພດທີ່ຝັງໄວ້ແມ່ນການປ່ຽນແປງຄັ້ງໃຫຍ່ (breaking change).
- ການປ່ຽນແທນປະເພດທີ່ຝັງໄວ້ ເຖິງວ່າຈະມີທາງເລືອກອື່ນທີ່ກົງກັບ interface ດຽວກັນກໍແມ່ນການປ່ຽນແປງຄັ້ງໃຫຍ່ (breaking change).

ເຖິງວ່າການຂຽນເມັດທອດທີ່ມອບໝາຍຈະໜ້າເບື່ອ ແຕ່ມັນຈະຊ່ວຍເຊື່ອງລາຍລະອຽດການໃຊ້ງານເຮັດໃຫ້ມີໂອກາດຫຼາຍຂຶ້ນໃນການປ່ຽນແປງ ແລະຍັງກຳຈັດຄວາມບໍ່ຊັດເຈນໃນການຄົ້ນຫາລາຍການ interface ໃນເອກະສານ.

## Avoid `init()`

ຫຼີກລ້ຽງ `init()` ຖ້າເປັນໄປໄດ້. ເມື່ອ `init()` ຫຼີກລ້ຽງບໍ່ໄດ້ ຫຼືຕ້ອງການ ໂຄ໋ດຄວນພະຍາຍາມ:

1. ຖືກກຳນົດໂດຍສົມບູບ ໂດຍບໍ່ຄຳນຶງເຖິງສະພາບແວດລ້ອມ ຫຼືການຮ້ອງຂໍຂອງໂປຣແກຣມ.
2. ຫຼີກລ້ຽງການຈັດລຳດັບ ຫຼືຜົນຂ້າງຄຽງຂອງຟັງຊັ່ນ `init()` ເຖິງວ່າການລ້ຽງລຳດັບຈະເປັນທີ່ຮູ້ຈັກກັນດິ ແຕ່ໂຄ໋ດກໍສາມາດປ່ຽນແປງໄດ້ ແລະໂດຍເຫດນີ້ຄວາມສຳພັນລະຫວ່າງຟັງຊັ່ນ `init()` ສາມາດເຮັດໃຫ້ໂຄ໋ດເບາະບາງ ແລະເກີດຂໍ້ຜິດພາດໄດ້ງ່າຍ.
3. ຫຼີກລ້ຽງການເຂົ້າເຖິງ ຫຼືຈັດການສະຖານະ ຫຼືສະພາບແວດລ້ອມລະດັບເທິງ ເຊັ່ນ: ຂໍ້ມູນຂອງເຄື່ອງ, ຕົວປ່ຽນສະພາບແວດລ້ອມ, ໄດເຣັກທໍຣີ, ອາກິວເມັນ/ອິນພຸດຂອງໂປຣແກຣມ.
4. ຫຼີກລ້ຽງ I/O, ລະບົບໄຟລ, ເຄືອຂ່າຍ ແລະ ການເອີ້ນໃຊ້ລະບົບ.

ໂຄ໋ດທີ່ບໍ່ຕອບສະໜອງຄວາມຕ້ອງການເຫຼົ່ານີ້ຈະເປັນຂອງຜູ້ຊ່ວຍ ເອີ້ນວ່າເປັນສ່ວນໜຶ່ງຂອງ `main()` (ຫຼືທີ່ອື່ນໆໃນວົງຈອນຊີວິດຂອງໂປຣແກຣມ) ຫຼືຂຽນເປັນສ່ວນໜຶ່ງຂອງ `main()` ເອງ ໂດຍສະເພາະໄລບາຣີ່ທີ່ມີຈຸດປະສົງຈະນຳໄປໃຊ້ໃນໂປຣແກຣມອື່ນຄວນລະມັດລະວັງເປັນພິເສດ ເພື່ອໃຫ້ໄດ້ຮັບການກຳນົດທີ່ສົມບູນ ແລະບໍ່ມີການ "init magic".

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Foo struct {
    // ...
}

var _defaultFoo Foo

func init() {
    _defaultFoo = Foo{
        // ...
    }
}
```

</td><td>

```go
var _defaultFoo = Foo{
    // ...
}

// or, better, for testability:

var _defaultFoo = defaultFoo()

func defaultFoo() Foo {
    return Foo{
        // ...
    }
}
```

</td></tr>
<tr><td>

```go
type Config struct {
    // ...
}

var _config Config

func init() {
    // Bad: based on current directory
    cwd, _ := os.Getwd()

    // Bad: I/O
    raw, _ := ioutil.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )

    yaml.Unmarshal(raw, &_config)
}
```

</td><td>

```go
type Config struct {
    // ...
}

func loadConfig() Config {
    cwd, err := os.Getwd()
    // handle err

    raw, err := ioutil.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )
    // handle err

    var config Config
    yaml.Unmarshal(raw, &config)

    return config
}
```

</td></tr>
</tbody></table>

ເມື່ອພິຈາລະນາຈາກຂ້າງເທິງ ບາງສະຖານະການທີ່ `init()` ດີກວ່າ ຫຼືຈຳເປັນລວມມີ:

- ນິພົດທີ່ຊັບຊ້ອນ ຊຶ່ງບໍ່ສາມາດແທນໄດ້ໂດຍການແທນຄ່າຄັ້ງດຽວ.
- Pluggable hooks ເຊັ່ນ `database/sql` dialects, ການລົງທະບຽນການເຂົ້າລະຫັດ (encoding type registries) ແລະອື່ນໆ.
- ການເພີ່ມປະສິດທິພາບໃຫ້ [Google Cloud Functions] ແລະຮູບແບບອື່ນໆຂອງການຄຳນວນລ່ວງໜ້າຕາມທີ່ກຳນົດ.

[google cloud functions]: https://cloud.google.com/functions/docs/bestpractices/tips#use_global_variables_to_reuse_objects_in_future_invocations

## Exit in Main

ໂປຣແກຣມ Go ໃຊ້ [`os.Exit`] ຫຼື [`log.Fatal*`] ໃນການອອກຈາກໂປຣແກຣມແບບທັນທີ. (Panicking ບໍ່ການວິທີການທີ່ດີໃນການອອກຈາກໂປຣແກຣມ ສະນັ້ນ [ຫ້າມ Panic](#dont-panic))

[`os.exit`]: https://golang.org/pkg/os/#Exit
[`log.fatal*`]: https://golang.org/pkg/log/#Fatal

ເອີ້ນໃຊ້ `os.Exit` ຫຼື `log.Fatal*` **ສະເພາະໃນ `main()` ເທົ່ານັ້ນ**. ທຸກໆຟັງຊັ່ນຄວນສົ່ງ error ອອກມາເພື່ອບອກວ່າມີຂໍ້ຜິດພາດເກີດຂຶ້ນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func main() {
  body := readFile(path)
  fmt.Println(body)
}

func readFile(path string) string {
  f, err := os.Open(path)
  if err != nil {
    log.Fatal(err)
  }

  b, err := ioutil.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }

  return string(b)
}
```

</td><td>

```go
func main() {
  body, err := readFile(path)
  if err != nil {
    log.Fatal(err)
  }
  fmt.Println(body)
}

func readFile(path string) (string, error) {
  f, err := os.Open(path)
  if err != nil {
    return "", err
  }

  b, err := ioutil.ReadAll(f)
  if err != nil {
    return "", err
  }

  return string(b), nil
}
```

</td></tr>
</tbody></table>

ເຫດຜົນ: ໂປຣແກຣທທີ່ມີຫຼາຍຟັງຊັ່ນທີ່ exit ອາດຈະມີບັນຫາບາງປະການ:

- ໂຟລການຄວບຄຸມບໍ່ຊັດເຈນ: ຟັງຊັ່ນໃດໆສາມາດອອກຈາກໂປຣແກຣມໄດ້ ດັ່ງນັ້ນຈື່ງຍາກທີ່ຈະໃຫ້ເຫດຜົນກ່ຽວກັບໂຟລການຄວບຄຸມ.
- ຍາກໃນການທົດສອຍ: ຟັງຊັ່ນທີ່ອອກຈາກໂປຣແກຣມຈະອອກຈາກການທົດສອບທີ່ເອີ້ນມັນເຊັ່ນກັນ ເຮັດໃຫ້ຟັງຊັ່ນນີ້ຍາກຕໍ່ການທົດສອບ ແລະເຮັດໃຫ້ສ່ຽງຕໍ່ການຂ້າມການທົດສອບອື່ນໆທີ່ຍັງບໍ່ທັນດຳເນີນການໂດຍ `go test`.
- ຂ້າມການລ້າງຂໍ້ມູນ (cleanup): ເມື່ອຟັງຊັ່ນອອກຈາກໂປຣແກຣມຟັງຊັ່ນຈະຂ້າມການເອີ້ນຟັງຊັ່ນທີ່ຈັດທຳໂດຍຄຳສັ່ງ `defer` ຊຶ່ງຈະເພີ່ມຄວາມສ່ຽງທີ່ຈະຂ້າມການລ້າງຂໍ້ມູນທີ່ສຳຄັນ.

### Exit Once

ຖ້າເປັນໄປໄດ້ໃຫ້ເອີ້ນ `os.Exit` ຫຼື `log.Fatal` **ພ້ອມກັນຫຼາຍຄັ້ງ** ໃນ `main()`. ຖ້າຫາກມີຂໍ້ຜິດພາດຫຼາຍສະຖານະການທີ່ຫຍຸດການເຮັດວຽກຂອງໂປຣແກຣມໃຫ້ວາງຕັກກະນັ້ນໄວ້ພາຍໃນຟັງຊັ່ນແຍກຕ່າງຫາກ.

ສິ່ງນີ້ມີຜົນຕໍ່ການຫຍໍ້ຟັງຊັ່ນ `main()` ແລະວາງຕັກກະທາງທຸລະກິດທີ່ສຳຄັນທັງໝົດໄວ້ໃນຟັງຊັ່ນແຍກຕ່າງຫາກທີ່ສາມາດທົດສອບໄດ້.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
package main

func main() {
  args := os.Args[1:]
  if len(args) != 1 {
    log.Fatal("missing file")
  }
  name := args[0]

  f, err := os.Open(name)
  if err != nil {
    log.Fatal(err)
  }
  defer f.Close()

  // ຖ້າເຮົາເອີ້ນ log.Fatal ຫຼັງຈາກແຖວນີ້,
  // f.Close ຈະບໍ່ຖືກເອີ້ນ.

  b, err := ioutil.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }

  // ...
}
```

</td><td>

```go
package main

func main() {
  if err := run(); err != nil {
    log.Fatal(err)
  }
}

func run() error {
  args := os.Args[1:]
  if len(args) != 1 {
    return errors.New("missing file")
  }
  name := args[0]

  f, err := os.Open(name)
  if err != nil {
    return err
  }
  defer f.Close()

  b, err := ioutil.ReadAll(f)
  if err != nil {
    return err
  }

  // ...
}
```

</td></tr>
</tbody></table>

# ປະສິດທິິພາບ (Performance)

ຄຳແນະນຳສະເພາະດ້ານປະສິດທິພາບໃຊ້ສະເພາະກັບ hot path ເທົ່ານັ້ນ.

## Prefer strconv over fmt

ເມື່ອແປງຄ່າພື້ນຖານໄປມາຈາກ string `strconv` ຈະໄວກວ່າໃຊ້ `fmt`.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  s := fmt.Sprint(rand.Int())
}
```

</td><td>

```go
for i := 0; i < b.N; i++ {
  s := strconv.Itoa(rand.Int())
}
```

</td></tr>
<tr><td>

```
BenchmarkFmtSprint-4    143 ns/op    2 allocs/op
```

</td><td>

```
BenchmarkStrconv-4    64.2 ns/op    1 allocs/op
```

</td></tr>
</tbody></table>

## Avoid string-to-byte conversion

ຢ່າສ້າງ slices ຂອງ byte ຈາກຂໍ້ຄວາມເກົ່າໃນຫຼູບ ໃຫ້ເຮັດຄັ້ງດຽວພໍ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  w.Write([]byte("Hello world"))
}
```

</td><td>

```go
data := []byte("Hello world")
for i := 0; i < b.N; i++ {
  w.Write(data)
}
```

</tr>
<tr><td>

```
BenchmarkBad-4   50000000   22.2 ns/op
```

</td><td>

```
BenchmarkGood-4  500000000   3.25 ns/op
```

</td></tr>
</tbody></table>

## Prefer Specifying Container Capacity

ລະບຸຄວາມຈຸຂອງ container ຫາກເປັນໄປໄດ້ ເພື່ອຈັດສັນໜ່ວຍຄວາມຈຳສຳລັບ container ໄວ້ລ່ວງໜ້າ ຊຶ່ງຈະຫຼຸດການຈັດສັນທີ່ຕາມມາ (ໂດຍການຄັດລອກ ແລະປັບຂະໜາດ) ເມື່ອມີອົງປະກອບເພີ່ມຂຶ້ນ.

### Specifying Map Capacity Hints

ຫາກເປັນໄປໄດ້ໃຫ້ບອກໃບ້ຂະໜາດໃຫ້ກັບ maps ເມື່ອເອີ້ນ `make()`

```go
make(map[T1]T2, hint)
```

ການໃບ້ຄ່າຄວາມຈຸກັບ `make()` ຢ່າງນ້ອຍໃຫ້ມັນໃກ້ຄຽງທີ່ສຸດຕອນທີ່ສ້າງ map ຈະຊ່ວຍຫຼຸດເວລາຕອນທີ່ຕ້ອງເພີ່ມຂະໜາດມັນຕາມຫຼັງ.

ໃຫ້ຮູ້ໄວ້ວ່າ map ບໍ່ຄືກັນກັບ slice ການໃສ່ຄວາມຈຸແບບນີ້ກໍບໍ່ໄດ້ຮັບປະກັນວ່າມັນຈະບໍ່ເສຍເວລາ ເພາະບາງທີ່ການເພີ່ມຂອງເຂົ້າໄປກໍອາດຈະເກີດຂະບວນການຈອງໜ່ວຍຄວາມຈຳໄດ້ ທັ້ງທີ່ກໍໄດ້ໃຫ້ຄວາມຈຸໄປກ່ອນໜ້າແລ້ວ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[string]os.FileInfo)

files, _ := ioutil.ReadDir("./files")
for _, f := range files {
    m[f.Name()] = f
}
```

</td><td>

```go

files, _ := ioutil.ReadDir("./files")

m := make(map[string]os.FileInfo, len(files))
for _, f := range files {
    m[f.Name()] = f
}
```

</td></tr>
<tr><td>

`m` ຖືກສ້າງໂດຍບໍ່ບອກຂະໜາດ ຊຶ່ງມັນອາດຈະຕ້ອງເສຍເວລາຈັງຫວະທີ່ຈະຈອງໜ່ວຍຄວາມຈຳ

</td><td>

`m` ຖືກສ້າງໂດຍບອກຂະໜາດ ຊຶ່ງຈະເສຍເວລາຈອງໜ່ວຍຄວາມຈຳນ້ອຍກວ່າ

</td></tr>
</tbody></table>

### Specifying Slice Capacity

ຫາກເປັນໄປໄດ້ໃຫ້ບອກໃບ້ຂະໜາດໃຫ້ກັບ slices ເມື່ອເອີ້ນ `make()` ໂດຍສະເພາະຢ່າງຍິ່ງເມື່ອໃຊ້ appending.

```go
make([]T, length, capacity)
```

ບໍ່ຄືກັບ maps, ຂະໜາດຂອງ slice ບໍ່ແມ່ນການໃບ້ ຕົວຄອມໄພລເລີຈະຈອງໜ່ວຍຄວາມຈຳໃຫ້ກັບ slice ຢ່າງພຽງພໍຕາມທີ່ເຮົາກຳນົດໃນ `make()` ໝາຍຄວາມວ່າການ `append()` ຈະບໍ່ມີການຈອງເພີ່ມ zero allocations (ຈົນກວ່າຄວາມຍາວຂອງ slice ຈະເຕັມຄວາມຈຸ ເຊິ່ງທຸກໆການ append ຈະຕ້ອງປ່ຽນແປງຂະໜາດເພີ່ມຮອງຮັບການເພີ່ມຂຶ້ນ)

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0, size)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td></tr>
<tr><td>

```
BenchmarkBad-4    100000000    2.48s
```

</td><td>

```
BenchmarkGood-4   100000000    0.21s
```

</td></tr>
</tbody></table>

# ຮູບແບບ (Style)

## Be Consistent

ຄຳແນະນຳບາງສ່ວນໃນເອກະສານຊຸດນີ້ສາມາດວັດຜົນໄດ້ຈິງ ເວັ້ນໄວ້ແຕ່ພຽງ ພຶດຕິກຳ, ບໍລິບົດ ຫຼືຫົວຂໍ້ຕ່າງໆ.

ນອກເໜືອຈາກທີ່ກ່າວມາຄື **ເຮັດໃຫ້ເປັນຮູບແບບດຽວກັນ**

ໂຄ໋ດທີ່ມີຮູບແບບດຽວກັນມັນເບິ່ງແຍງຮັກສາງ່າຍ, ເຂົ້າໃຈງ່າຍ, ບໍ່ເສຍເວລາມານັ່ງແກະ ແລ້ວຖ້າເວລາແກ້ໄຂຍ້າຍທີ່ມັນກໍຍັງເຮັດໄດ້ງ່າຍກວ່າ ລວມທັງຕອນແກ້ບັ້ກນຳ.

ກົງກັນຂ້າມ ຖ້າຂຽນຄົນລະຮູບແບບທັງທີ່ເປັນໂຄ໋ດຊຸດດຽວກັນ ມັນຈະເຮັດໃຫ້ເສຍເວລາໃນການເບິ່ງແຍງ, ເບາະບາງ ແລະບໍ່ເຂົ້າກັນ. ທັງໝົດນີ້ຈະເຮັດໃຫ້ເຮັດວຽກຊ້າ, ຣີວິວຍາກ ແລະເກີດບັ້ກງ່າຍ.

ເວລາຈະນຳເອົາຄຳແນະນຳນີ້ໄປປັບໃຊ້ ແນະນຳວ່າໃຫ້ເຮັດໃນລະດັບແພັກເກັດ (ຫຼືໃຫຍ່ກວ່າ) ຖ້າເຮັດໃນແພັກເກັດຍ່ອຍໆມັນຈະຂັດກັບສິ່ງທີ່ກ່າວມາຂ້າງຕົ້ນ ເພາະມັນຈະມີຫຼາຍຮູບແບບໃນໂຄ໋ດຊຸດດຽວ.

## Group Similar Declarations

Go ສະໜັບສະໜູນການຈັດກຸ່ມການປະກາດທີ່ເປັນພວກດຽວກັນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
import "a"
import "b"
```

</td><td>

```go
import (
  "a"
  "b"
)
```

</td></tr>
</tbody></table>

ການເຮັດແບບນີ້ຍັງສາມາດໃຊ້ໄດ້ກັບການປະກາດ ຄ່າຄົງທີ່, ຕົວປ່ຽນ ແລະການປະກາດປະເພດ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go

const a = 1
const b = 2



var a = 1
var b = 2



type Area float64
type Volume float64
```

</td><td>

```go
const (
  a = 1
  b = 2
)

var (
  a = 1
  b = 2
)

type (
  Area float64
  Volume float64
)
```

</td></tr>
</tbody></table>

ຈັດກຸ່ມສະເພາະສິ່ງທີ່ສຳພັນກັນ ຢ່າໄປເຮັດກັບສິ່ງທີ່ບໍ່ກ່ຽວຂ້ອງກັນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
  EnvVar = "MY_ENV"
)
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

const EnvVar = "MY_ENV"
```

</td></tr>
</tbody></table>

ການຈັດກຸ່ມສາມາດເຮັດໃນຟັງຊັ່ນກໍໄດ້.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func f() string {
  var red = color.New(0xff0000)
  var green = color.New(0x00ff00)
  var blue = color.New(0x0000ff)

  ...
}
```

</td><td>

```go
func f() string {
  var (
    red   = color.New(0xff0000)
    green = color.New(0x00ff00)
    blue  = color.New(0x0000ff)
  )

  ...
}
```

</td></tr>
</tbody></table>

## Import Group Ordering

ແບ່ງກຸ່ມການນຳເຂົ້າເປັນສອງຊຸດ:

- Standard library
- Everything else

ການຈັດກຸ່ມນີ້ `goimports` ເຮັດໃຫ້ເຮົາຢູ່ແລ້ວ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"
  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td><td>

```go
import (
  "fmt"
  "os"

  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td></tr>
</tbody></table>

## Package Names

ເວລາຈະປະກາດຊື່ແພັກເກັດໃຫ້ເລືອກແບບນີ້:

- ໃຊ້ຕົວອັກສອນນ້ອຍທັງໝົດ ບໍ່ມີຕົວໃຫຍ່ ຫຼືຂີດກ້ອງ.
- ບໍ່ປ່ຽນຊື່ມັນຕອນທີ່ຜູ້ໃຊ້ import ມັນເຂົ້າໄປ.
- ສັ້ນ ແລະກະທັດເຮັດ ເພາະມັນຈະຖືກອ້າງອີງໃນທຸກທີ່ຈະມາເອີ້ນໃຊ້.
- ບໍ່ຕ້ອງເປັນພະຫຸພົດ ຕົວຢ່າງ `net/url` ບໍ່ແມ່ນ `net/urls`.
- ຢ່າຕັ້ງຊື່ "common", "util", "shared", ຫຼື "lib" ຊື່ພວກນີ້ມັນບໍ່ຊ່ວຍໃຫ້ເຮົາຮູ້ຫຍັງເລີຍ.

ເບິ່ງເພີ່ມເຕີມ [Package Names] ແລະ [Style guideline for Go packages].

[package names]: https://blog.golang.org/package-names
[style guideline for go packages]: https://rakyll.org/style-packages/

## Function Names

ເຮົາເຮັດແບບດຽວກັນກັບ Go community ເຮັດກັນດ້ວຍການໃຊ້ [MixedCaps for function
names] (ການປະສົມຕົວອັກສອນນ້ອຍແລະໃຫຍ່) ຍົກເວັ້ນສະເພາະເວລາຂຽນທົດສອບສາມາດໃຊ້ຂີດກ້ອງໄດ້ ເພື່ອຈັດກຸ່ມການທົດສອບທີ່ສຳພັນກັນ ຕົວຢ່າງ `TestMyFunction_WhatIsBeingTested`.

[mixedcaps for function names]: https://golang.org/doc/effective_go.html#mixed-caps

## Import Aliasing

ເຮົາຕັ້ງຊື່ນາມແຝງໃຫ້ແພັກເກັດທີ່ນຳເຂົ້າ ກໍຕໍ່ເມື່ອແພັດເກັດທີ່ນຳເຂົ້າມາບໍ່ຕົງກັນກັບສ່ວນສຸດທ້າຍຂອງ path.

```go
import (
  "net/http"

  client "example.com/client-go"
  trace "example.com/trace/v2"
)
```

ໃນກໍລະນີອື່ນໆ ການຕັ້ງຊື່ນາມແຝງໃຫ້ການນຳເຂົ້າບໍ່ຄວນເຮັດ ເວັ້ນເສຍແຕ່ວ່າມັນຈະໄປຊ້ຳກັບແພັກເກັດອື່ນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"


  nettrace "golang.net/x/trace"
)
```

</td><td>

```go
import (
  "fmt"
  "os"
  "runtime/trace"

  nettrace "golang.net/x/trace"
)
```

</td></tr>
</tbody></table>

## Function Grouping and Ordering

- ຄວນລຽງຟັງຊັ່ນຕາມລຳດັບການເອີ້ນໃຊ້.
- ຟັງຊັ່ນໃນໄຟລຄວນຈັດກຸ່ມຕາມຕົວຮັບ (receiver).

ຟັງຊັ່ນທີ່ເປີດເຜີຍໄປພາຍນອກຄວນຢູ່ໃນສ່ວນຫົວຂອງໄຟລ ຫຼັງການປະກາດ `struct`, `const`, `var`.

ຟັງຊັ່ນແບບນີ້ `newXYZ()`/`NewXYZ()` ຄວນຢູ່ຫຼັງການປະກາດປະເພດ ແຕ່ຢູ່ກ່ອນເມັດທອດທີ່ໃຊ້ປະເພດນີ້ເປັນຕົວຮັບ.

ເມື່ອຟັງຊັ່ນຖືກຈັດກຸ່ມດ້ວຍຕົວຮັບແບບນີ້ ພວກຟັງຊັ່ນດິບທີ່ໃຊ້ງານທົ່ວໄປກໍຄວນຢູ່ສ່ວນທ້າຍຂອງໄຟລ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func (s *something) Cost() {
  return calcCost(s.weights)
}

type something struct{ ... }

func calcCost(n []int) int {...}

func (s *something) Stop() {...}

func newSomething() *something {
    return &something{}
}
```

</td><td>

```go
type something struct{ ... }

func newSomething() *something {
    return &something{}
}

func (s *something) Cost() {
  return calcCost(s.weights)
}

func (s *something) Stop() {...}

func calcCost(n []int) int {...}
```

</td></tr>
</tbody></table>

## Reduce Nesting

ໂຄ໋ດຄວນຫຼຸດຄວາມຊັບຊ້ອນດ້ວຍການຈັດການ error ກ່ອນແລ້ວສົ່ງຄ່າອອກໄປ ຫຼືໄປເລີ່ມຕົ້ນຫຼູບໃໝ່ໃຫ້ໄວທີ່ສຸດ ເພື່ອຫຼຸດໂຄ໋ດທີ່ຊ້ອນກັນຫຼາຍໆຊັ້ນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
for _, v := range data {
  if v.F1 == 1 {
    v = process(v)
    if err := v.Call(); err == nil {
      v.Send()
    } else {
      return err
    }
  } else {
    log.Printf("Invalid v: %v", v)
  }
}
```

</td><td>

```go
for _, v := range data {
  if v.F1 != 1 {
    log.Printf("Invalid v: %v", v)
    continue
  }

  v = process(v)
  if err := v.Call(); err != nil {
    return err
  }
  v.Send()
}
```

</td></tr>
</tbody></table>

## Unnecessary Else

ຖ້າຕົວປ່ຽນຈະຖືກກຳນົດຄ່າທັງໃນ if ແລະ else ມັນຄວນຈະເຫຼືອແຕ່ if ກໍໄດ້.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var a int
if b {
  a = 100
} else {
  a = 10
}
```

</td><td>

```go
a := 10
if b {
  a = 100
}
```

</td></tr>
</tbody></table>

## Top-level Variable Declarations

ການໃຊ້ຄີເວີດ `var` ບໍ່ຕ້ອງບອກປະເພດກໍໄດ້ ເວັ້ນເສຍແຕ່ມັນຈະຄືນປະເພດທີ່ບໍ່ຕົງກັບທີ່ຕ້ອງການ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var _s string = F()

func F() string { return "A" }
```

</td><td>

```go
var _s = F()
// Since F already states that it returns a string, we don't need to specify
// the type again.

func F() string { return "A" }
```

</td></tr>
</tbody></table>

ລະບຸປະເພດ ຖ້າປະເພດທີ່ໄດ້ຮັບມາບໍ່ຕົງກັບທີ່ຢາກໄດ້.

```go
type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F returns an object of type myError but we want error.
```

## Prefix Unexported Globals with \_

ຕັ້ງຊື່ຂຶ້ນຕົ້ນດ້ວຍ ຂີດກ້ອງ ເວລາປະກາດດ້ວຍ `var`s ແລະ `const`s ໃຫ້ຕົວປ່ຽນທີ່ບໍ່ໄດ້ເປີດເຜີຍສູ່ພາຍນອກ ເພື່ອເຮັດໃຫ້ຊັດເຈນວ່າມັນຖືກໃຊ້ເປັນ global ຢູ່ໃນແພັກເກັດ.

ຂໍ້ຍົກເວັ້ນ: ຕົວປ່ຽນ error ທີ່ບໍ່ໄດ້ເປີດເຜີຍສູ່ພາຍນອກ ຄວນຕັ້ງຊື່ຂຶ້ນຕົ້ນດ້ວຍ err.

ຫຼັກການ ແລະເຫດຜົນ: ຕົວປ່ຽນທີ່ປະກາດໄວ້ຕັ້ງແຕ່ຕົ້ນ ແລະພວກຄ່າຄົງທີ່ມີຂອບເຂດໃນແພັກເກັດ ເພາະສະນັ້ນ ການຕັ້ງຊື່ແບບກາງໆມັນຈະເຮັດໃຫ້ເກີດເລື່ອງບໍ່ຄາດຄິດໄດ້ ເຮັດໃຫ້ໄດ້ຄ່າຜິດໃນໄຟລອື່ນໄດ້ງ່າຍ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// foo.go

const (
  defaultPort = 8080
  defaultUser = "user"
)

// bar.go

func Bar() {
  defaultPort := 9090
  ...
  fmt.Println("Default port", defaultPort)

  // We will not see a compile error if the first line of
  // Bar() is deleted.
}
```

</td><td>

```go
// foo.go

const (
  _defaultPort = 8080
  _defaultUser = "user"
)
```

</td></tr>
</tbody></table>

## Embedding in Structs

ປະເພດທີ່ຖືກຝັງໄວ້ (ເຊັ່ນ mutexes) ຄວນຢູ່ເທິງສຸດຂອງການຂອງຟິວໃນ struct ແລະຄວນເວັ້ນແຖວຫວ່າງໆໄວ້

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type Client struct {
  version int
  http.Client
}
```

</td><td>

```go
type Client struct {
  http.Client

  version int
}
```

</td></tr>
</tbody></table>

ການຝັງຄວນໃຫ້ປະໂຫຍດທີ່ຈັບຕ້ອງໄດ້ເຊັ່ນ ການເພີ່ມ ຫຼືຂະຫຍາຍການເຮັດວຽກໃນວິທີທີ່ເໝາະສົມກັບຄວາມໝາຍ (semantically-appropriate) ຄວນເຮັດສິ່ງນີ້ໂດຍບໍ່ມີຜົນກະທົບທີ່ບໍ່ພຶງປະສົງຈາກຜູ້ໃຊ້ ເບິ່ງເພີ່ມເຕີມ [avoid embedding types in public structs]

[avoid embedding types in public structs]: #avoid-embedding-types-in-public-structs

ການຝັງ **ບໍ່ຄວນ**:

- ເນັ້ນຄວາມສະດວກສະບາຍຢ່າງແທ້ຈິງ.
- ເຮັດໃຫ້ປະເພດພາຍນອກສ້າງ ຫຼືໃຊ້ງານໄດ້ຍາກຂຶ້ນ.
- ສົ່ງຜົນກະທົບຕໍ່ຄ່າສູນປະເພດພາຍນອກ ຫາກປະເພດພາຍນອກມີຄ່າສູນເປັນຄ່າທີ່ຖືກຕ້ອງ ມັນຄວນຈະຍັງຖືກຕ້ອງຫຼັງຈາກຝັງປະເພດພາຍໃນເຂົ້າໄປ.
- ເປີດເຜີຍຟັງຊັ່ນ ຫຼືຟິວທີ່ບໍ່ກ່ຽວຂ້ອງຈາກປະເພດພາຍນອກເປັນຜົນຂ້າງຄຽງຂອງການຝັງປະເພດພາຍໃນ.
- ເປີດເຜີຍປະເພດທີ່ບໍ່ໄດ້ສົ່ງອອກ.
- ສົ່ງຜົນຕໍ່ຄວາມໝາຍການຄັດລອກປະເພດພາຍນອກ.
- ປ່ຽນ API ຂອງປະເພດພາຍນອກ ຫຼືປະເພດຄວາມໝາຍ (semantics).
- ຝັງຮູບແບບທີ່ບໍ່ບັນຍັດຂອງພາຍໃນ (non-canonical).
- ເປີີດເຜີຍລາຍລະອຽດການໃຊ້ງານພາຍນອກ.
- ອະນຸຍາດໃຫ້ຜູ້ໃຊ້ສັງເກດການ ຫຼືຄວບຄຸມປະເພດພາຍໃນ.
- ປ່ຽນລັກສະນະການທຳງານທົ່ວໄປຂອງຟັງຊັ່ນພາຍໃນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
type A struct {
    // Bad: A.Lock() and A.Unlock() are
    //      now available, provide no
    //      functional benefit, and allow
    //      users to control details about
    //      the internals of A.
    sync.Mutex
}
```

</td><td>

```go
type countingWriteCloser struct {
    // Good: Write() is provided at this
    //       outer layer for a specific
    //       purpose, and delegates work
    //       to the inner type's Write().
    io.WriteCloser

    count int
}

func (w *countingWriteCloser) Write(bs []byte) (int, error) {
    w.count += len(bs)
    return w.WriteCloser.Write(bs)
}
```

</td></tr>
<tr><td>

```go
type Book struct {
    // Bad: pointer changes zero value usefulness
    io.ReadWriter

    // other fields
}

// later

var b Book
b.Read(...)  // panic: nil pointer
b.String()   // panic: nil pointer
b.Write(...) // panic: nil pointer
```

</td><td>

```go
type Book struct {
    // Good: has useful zero value
    bytes.Buffer

    // other fields
}

// later

var b Book
b.Read(...)  // ok
b.String()   // ok
b.Write(...) // ok
```

</td></tr>
<tr><td>

```go
type Client struct {
    sync.Mutex
    sync.WaitGroup
    bytes.Buffer
    url.URL
}
```

</td><td>

```go
type Client struct {
    mtx sync.Mutex
    wg  sync.WaitGroup
    buf bytes.Buffer
    url url.URL
}
```

</td></tr>
</tbody></table>

## Local Variable Declarations

ການປະກາດຕົວປ່ຽນແບບສັ້ນ (`:=`) ຄວນຖືກໃຊ້ເມື່ອຕ້ອງການກຳນົດຄ່າໃຫ້ຕົວປ່ຽນຢູ່ແລ້ວ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var s = "foo"
```

</td><td>

```go
s := "foo"
```

</td></tr>
</tbody></table>

ຢ່າງໃດກໍດີ ບາງກໍລະນີການປ່ອຍໃຫ້ມັນເປັນຄ່າເລີ່ມຕົ້ນກໍອາດຈະຊັດເຈນກວ່າ ດ້ວຍການໃຊ້ຄີເວິດ `var` [Declaring Empty Slices] ຕົວຢ່າງ:

[declaring empty slices]: https://github.com/golang/go/wiki/CodeReviewComments#declaring-empty-slices

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func f(list []int) {
  filtered := []int{}
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td><td>

```go
func f(list []int) {
  var filtered []int
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td></tr>
</tbody></table>

## nil is a valid slice

`nil` ເປັນຄ່າທີ່ເໝາະສົມທີ່ຈະໃຊ້ແທນ slice ຂະໜາດ 0 ໝາຍຄວາມວ່າ

- ເຈົ້າບໍ່ຄວນສົ່ງ slice ທີ່ມີຂະໜາດສູນອອກໄປຕົງໆ ແຕ່ໃຫ້ສົ່ງ `nil` ອອກໄປແທນ.

  <table>
  <thead><tr><th>Bad</th><th>Good</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  if x == "" {
    return []int{}
  }
  ```

  </td><td>

  ```go
  if x == "" {
    return nil
  }
  ```

  </td></tr>
  </tbody></table>

- ການກວດສອບວ່າ slice ນັ້ນຫວ່າງຫຼືບໍ່ ໃຫ້ໃຊ້ `len(s) == 0` ຢ່າໄປກວດສອບ `nil`

  <table>
  <thead><tr><th>Bad</th><th>Good</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  func isEmpty(s []string) bool {
    return s == nil
  }
  ```

  </td><td>

  ```go
  func isEmpty(s []string) bool {
    return len(s) == 0
  }
  ```

  </td></tr>
  </tbody></table>

- ຄ່າສູນ (slice ที่ประกาศด้วย `var`) ສາມາດໃຊ້ງານໄດ້ເລີຍ ໂດຍບໍ່ຕ້ອງ `make()` ກ່ອນ.

  <table>
  <thead><tr><th>Bad</th><th>Good</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  nums := []int{}
  // or, nums := make([]int)

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td><td>

  ```go
  var nums []int

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td></tr>
  </tbody></table>

ເຖິງວ່າ nil slice ຈະຖືກຕ້ອງ ແຕ່ມັນຈະບໍ່ມີຄ່າທຽບເທົ່າກັບຄວາມຍາວທີ່ຈັດສັນໄວ້ເປັນສູນ -ອັນໜຶ່ງເປັນ nil ແລະອີກອັນບໍ່ແມ່ນ- ແລະທັງສອງຈະໄດ້ຮັບການປະຕິບັດທີ່ແຕກຕ່າງກັນໃນສະຖານະການທີ່ແຕກຕ່າງກັນ (ເຊັ່ນ serialization).

## Reduce Scope of Variables

ຖ້າມີໂອກາດຫຼຸດຂອບເຂດຂອງຕົວປ່ຽນກໍຄວນເຮັດ ແຕ່ຢ່າໄປຫຼຸດມັນຖ້າມັນຂັດແຍ້ງກັບ [Reduce Nesting](#reduce-nesting)

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
err := ioutil.WriteFile(name, data, 0644)
if err != nil {
 return err
}
```

</td><td>

```go
if err := ioutil.WriteFile(name, data, 0644); err != nil {
 return err
}
```

</td></tr>
</tbody></table>

ຖ້າເຈົ້າຕ້ອງການຜົນຮັບຂອງຟັງຊັ່ນໄປໃຊ້ຫຼັງ if ຕໍ່ ເຊັ່ນນັ້ນເຈົ້າກໍບໍ່ຄວນຫຼຸດຂອບເຂດມັນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
if data, err := ioutil.ReadFile(name); err == nil {
  err = cfg.Decode(data)
  if err != nil {
    return err
  }

  fmt.Println(cfg)
  return nil
} else {
  return err
}
```

</td><td>

```go
data, err := ioutil.ReadFile(name)
if err != nil {
   return err
}

if err := cfg.Decode(data); err != nil {
  return err
}

fmt.Println(cfg)
return nil
```

</td></tr>
</tbody></table>

## Avoid Naked Parameters

ພາລາເມັດເຕີເປືອຍໆທີ່ໃສ່ໄປຕອນທີ່ເອີ້ນຟັງຊັ່ນມັນອ່ານຍາກ ໃຫ້ເພີ່ມຄອມເມັ້ນແບບ C-style ລົງໄປ (`/* ... */`) ເພື່ອໃຫ້ຄວາມໝາຍຊັດເຈນຂຶ້ນ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true, true)
```

</td><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true /* isLocal */, true /* done */)
```

</td></tr>
</tbody></table>

ແຕ່ມັນກໍຍັງບໍ່ດີທີ່ສຸດ ເຮົາຄວນແທນທີ່ປະເພດ `bool` ທີ່ເປືອຍໆຢູ່ນີ້ດ້ວຍການສ້າງປະເພດຂຶ້ນມາໃຫ້ມັນອ່ານງ່າຍຂຶ້ນ ແລະຍັງຮອງຮັບຫາກໃນອານາຄົດຕ້ອງການມີຫຼາຍສອງສະຖານະ (true/false)

```go
type Region int

const (
  UnknownRegion Region = iota
  Local
)

type Status int

const (
  StatusReady Status = iota + 1
  StatusDone
  // Maybe we will have a StatusInProgress in the future.
)

func printInfo(name string, region Region, status Status)
```

## Use Raw String Literals to Avoid Escaping

Go ສະໜັບສະໜຸນ [raw string literals](https://golang.org/ref/spec#raw_string_lit) ຊຶ່ງສາມາດໃສ່ຫຼາຍໆແຖວລວມທັງເຄື່ອງໝາຍຄຳເວົ້ານຳ ຊຶ່ງການໃຊ້ແບບນີ້ເພື່ອປ້ອງກັນການເຮັດ hand-escaped ເພາະມັນຈະເຮັດໃຫ້ອ່ານຍາກ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
wantError := "unknown name:\"test\""
```

</td><td>

```go
wantError := `unknown error:"test"`
```

</td></tr>
</tbody></table>

## Initializing Structs

### Use Field Names to Initialize Structs

ເຈົ້າຄວນກຳນົດຊື່ຟິວທຸກຕັ້ງເມື່ອກຳນົດຄ່າ struct ເຊິ່ງຖືກບັງຄັບໂດຍ [`go vet`].

[`go vet`]: https://golang.org/cmd/vet/

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
k := User{"John", "Doe", true}
```

</td><td>

```go
k := User{
    FirstName: "John",
    LastName: "Doe",
    Admin: true,
}
```

</td></tr>
</tbody></table>

ຂໍ້ຍົກເວັ້ນ: ຊື່ຟິວສາມາດລະເວັ້ນໄດ້ໃນຕາຕາລາງທົດສອບທີ່ມີ 3ຟິວ ຫຼືໜ້ອຍກວ່າ.

```go
tests := []struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}
```

### Omit Zero Value Fields in Structs

ເມື່ອກຳນົດຄ່າ struct ດ້ວຍຊື່ ໃຫ້ລະເວັ້ນຟິວທີ່ມີຄ່າສູນ (zero values) ເວັ້ນແຕ່ຈະລະບຸບໍລິບົດທີ່ມີຄວາມໝາຍ ບໍ່ດັ່ງນັ້ນໃຫ້ Go ກຳນົດຄ່າເຫຼົ່ານີ້ເປັນຄ່າສູນໃຫ້ອັດຕະໂນມັດ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
  MiddleName: "",
  Admin: false,
}
```

</td><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
}
```

</td></tr>
</tbody></table>

ວິທີນີ້ຈະຊ່ວຍຫຼຸດການລົບກວນ (noise) ຜູ້ອ່ານໂດຍການລະເວັ້ນຄ່າທີ່ເປັນຄ່າເລີ່ມຕົ້ນໃນບໍລິບົດນັ້ນ. ກຳນົດສະເພາະຄ່າທີ່ມີຄວາມໝາຍເທົ່ານັ້ນ.

ລວມທັງຄ່າສູນ ໂດຍຊື່ຟິວໃຫ້ບໍລິບົດທີ່ມີຄວາມໝາຍຊັດເຈນ ຕົວຢ່າງເຊັ່ນ [Test Tables](#test-tables) ຈະໄດ້ຮັບຜົນປະໂຫຍດຈາກຊື່ຂອງຟິວເຖີງວ່າຈະເປັນຄ່າສູນກໍຕາມ.

```go
tests := []struct{
  give string
  want int
}{
  {give: "0", want: 0},
  // ...
}
```

## Initializing Maps

ແນະນຳໃຫ້ໃຊ້ `make(..)` ເພື່ອສ້າງ maps ວ່າງໆ ແລະເອົາໄປຂຽນໂປຣແກຣມຕໍ່ໄດ້ ຊຶ່ງມັນທຳການປະກາດຕົວປ່ຽນໃຫ້ພ້ອມໃຊ້ງານ ເຊິ່ງແຕກຕ່າງຈາກການປະກາດລ້າໆ ແລະມັນເຮັດໃຫ້ງ່າຍຕໍ່ການເພີ່ມການໃບ້ຂະໜາດໃຫ້ໃນພາຍຫຼັງ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var (
  // m1 is safe to read and write;
  // m2 will panic on writes.
  m1 = map[T1]T2{}
  m2 map[T1]T2
)
```

</td><td>

```go
var (
  // m1 is safe to read and write;
  // m2 will panic on writes.
  m1 = make(map[T1]T2)
  m2 map[T1]T2
)
```

</td></tr>
<tr><td>

ການປະກາດໃຫ້ພ້ອມໃຊ້ງານ ແລະການປະກາດແລ້ວບໍ່ພ້ອມໃຊ້ງານ ເບິ່ງຄືໆກັນ.

</td><td>

ການປະກາດໃຫ້ພ້ອມໃຊ້ງານ ແລະການປະກາດແລ້ວບໍ່ພ້ອມໃຊ້ງານ ເບິ່ງແຕກຕ່າງກັນ.

</td></tr>
</tbody></table>

ຖ້າເຮັດໄດ້ກໍໃຫ້ໃບ້ຄວາມຈຸຕອນທີ່ປະກາດ maps ດ້ວຍຄຳສັ່ງ `make()` ເບິ່ງທີ່ [Specifying Map Capacity Hints](#specifying-map-capacity-hints) ສຳລັບຂໍ້ມູນເພີ່ມເຕີມ.

ໃນທາງກັບກັນ ຖ້າ map ນັ້ນຈະຕ້ອງເກັບຄ່າທີ່ແນ່ນອນກໍໃຫ້ໃຊ້ການປະກາດດ້ວຍປີກກາໄດ້ເລີຍ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[T1]T2, 3)
m[k1] = v1
m[k2] = v2
m[k3] = v3
```

</td><td>

```go
m := map[T1]T2{
  k1: v1,
  k2: v2,
  k3: v3,
}
```

</td></tr>
</tbody></table>

ກົດພື້ນຖານຫຼັກໆກໍຄື ໃຊ້ປີກກາປະກາດເມື່ອຕ້ອງໃສ່ຄ່າຄົງທີ່ລົງໄປຕັ້ງແຕ່ຕົ້ນ ບໍ່ເຊັ່ນນັ້ນກໍໃຊ້ `make` (ແລະໃບ້ຄວາມຈຸຖ້າເຮັດໄດ້)

## Format Strings outside Printf

ຖ້າເຈົ້າປະກາດການຈັດຮູບແບບຂໍ້ຄວາມສຳລັບໃຊ້ກັບຟັງຊັ່ນ `Printf`-style ໃຫ້ເຮັດເປັນ `ຄ່າຄົງທີ່`.

ມັນຈະຊ່ວຍໃຫ້ `go vet` ໄດ້ວິເຄາະການຈັດຮູບແບບໃຫ້.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
msg := "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td><td>

```go
const msg = "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td></tr>
</tbody></table>

## Naming Printf-style Functions

ເມື່ອເຈົ້າປະກາດຟັງຊັ່ນ `Printf`-style ຊ່ວຍເຮັດໃຫ້ໝັ້ນໃຈວ່າ `go vet` ຈະສາມາດກວດພົບມັນ ແລະຈະໄດ້ກວດສອບຮູບແບບຂໍ້ຄວາມໄດ້.

ໝາຍຄວາມວ່າ ເຈົ້າຄວນໃຊ້ຊື່ຕາມທີ່ຕັ້ງໄວ້ແລ້ວຕາມ `Printf`-style ຫາກເຮັດໄດ້. `go vet` ຈະໄດ້ກວດສອບຮູບແບບຂໍ້ຄວາມໄດ້ ເບິ່ງເພີ່ມເຕີມທີ່ [Printf family].

[printf family]: https://golang.org/cmd/vet/#hdr-Printf_family

ຖ້າການໃຊ້ຊື່ທີ່ຕັ້ງໄວ້ບໍ່ແມ່ນທາງເລືອກ ກໍໃຫ້ຕັ້ງຊື່ລົງທ້າຍດ້ວຍ f: ເຊັ່ນ `Wrapf` ບໍ່ແມ່ນ `Wrap`. ໂດຍສາມາດບອກໃຫ້ `go vet` ກວດສອບຟັງຊັ່ນ `Printf`-style ໄດ້ ແຕ່ມັນຈະຕ້ອງລົງທ້າຍດ້ວຍ f ເທົ່ານັ້ນ.

```shell
$ go vet -printfuncs=wrapf,statusf
```

ເບິ່ງເພີ່ມເຕີມ [go vet: Printf family check].

[go vet: printf family check]: https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/

# Patterns

## Test Tables

ໃຊ້ການທົດສອບທີ່ຄັບເຄື່ອນດ້ວຍຕາລາງ (table-driven tests) ດ້ວຍ [subtests] ເພື່ອຫຼີກລ້ຽງການຂຽນໂຄ໋ດຊ້ຳໆ ເວລາທີ່ເຮົາທົດສອບດ້ວຍລໍຈິກເກົ່າ.

[subtests]: https://blog.golang.org/subtests

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestSplitHostPort(t *testing.T)

host, port, err := net.SplitHostPort("192.0.2.0:8000")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("192.0.2.0:http")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "http", port)

host, port, err = net.SplitHostPort(":8000")
require.NoError(t, err)
assert.Equal(t, "", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("1:8")
require.NoError(t, err)
assert.Equal(t, "1", host)
assert.Equal(t, "8", port)
```

</td><td>

```go
// func TestSplitHostPort(t *testing.T)

tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  {
    give:     "192.0.2.0:8000",
    wantHost: "192.0.2.0",
    wantPort: "8000",
  },
  {
    give:     "192.0.2.0:http",
    wantHost: "192.0.2.0",
    wantPort: "http",
  },
  {
    give:     ":8000",
    wantHost: "",
    wantPort: "8000",
  },
  {
    give:     "1:8",
    wantHost: "1",
    wantPort: "8",
  },
}

for _, tt := range tests {
  t.Run(tt.give, func(t *testing.T) {
    host, port, err := net.SplitHostPort(tt.give)
    require.NoError(t, err)
    assert.Equal(t, tt.wantHost, host)
    assert.Equal(t, tt.wantPort, port)
  })
}
```

</td></tr>
</tbody></table>

ຕາຕາລາງການທົດສອບຊ່ວຍເຮັດໃຫ້ງ່າຍຕໍ່ການເພີ່ມບໍລິບົດ (context) ໃຫ້ error message ຫຼຸດໂຄ໋ດທີ່ຊ້ຳຊ້ອນ ແລະງ່າຍຕໍ່ການເພີ່ມຊຸດການທົດສອບ (test case).

ເຮົາປະຕິບັດຕາມທຳນຽມປະຕິບັດດ້ວຍການໃຊ້ slice ຂອງ struct ແລ້ວຕັ້ງຊື່ວ່າ `tests` ແລະແຕ່ລະ test case ໃຫ້ຊື່ `tt`. ລະບຸຊື່ໃຫ້ input ແລະ output ໃນລະແຕ່ test case ດ້ວຍການຕັ້ງຊື່ຂຶ້ນຕົ້ນວ່າ `give` ແລະ `want`.

```go
tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  // ...
}

for _, tt := range tests {
  // ...
}
```

## Functional Options

Functional options ເປັນຮູບແບບທີ່ໃຊ້ປະກາດ type `Option` ເພື່ອບັນທຶກຂໍ້ມູນລົງໄປໃນ struct ພາຍໃນ ຈາກນັ້ນໃຫ້ຮັບຕົວປ່ຽນແບບ varidic ເຂົ້າມາເປັນຕົວເລືອກ ແລະຈັດການຕາມຂໍ້ມູນທີ່ບັນທຶກໄວ້ໃນ options ທີ່ເປັນ struct ພາຍໃນ.

ໃຊ້ຮູບແບບນີ້ສຳລັບອາກິວເມັ້ນທີ່ເປັນຕົວເລືອກ ແລະ APIs ສາທາລະນະອື່ນໆທີ່ເຈົ້າຄາດເດົາໄດ້ວ່າຈະຕ້ອງຖືກຂະຫຍາຍ ໂດຍສະເພາະຢ່າງຍິ່ງຖ້າເຈົ້າມີອາກິວເມັ້ນ 3ຕົວ ຫຼືຫຼາຍກວ່າຢູ່ແລ້ວ.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
// package db

func Open(
  addr string,
  cache bool,
  logger *zap.Logger
) (*Connection, error) {
  // ...
}
```

</td><td>

```go
// package db

type Option interface {
  // ...
}

func WithCache(c bool) Option {
  // ...
}

func WithLogger(log *zap.Logger) Option {
  // ...
}

// Open creates a connection.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  // ...
}
```

</td></tr>
<tr><td>

The cache and logger parameters must always be provided, even if the user
wants to use the default.

```go
db.Open(addr, db.DefaultCache, zap.NewNop())
db.Open(addr, db.DefaultCache, log)
db.Open(addr, false /* cache */, zap.NewNop())
db.Open(addr, false /* cache */, log)
```

</td><td>

Options are provided only if needed.

```go
db.Open(addr)
db.Open(addr, db.WithLogger(log))
db.Open(addr, db.WithCache(false))
db.Open(
  addr,
  db.WithCache(false),
  db.WithLogger(log),
)
```

</td></tr>
</tbody></table>

ວິທີທີ່ເຮົາແນະນຳໃນການນຳຮູບແບບນີ້ໄປໃຊ້ຄືການໃຊ້ interface `Option` ທີ່ເກັບວິທີການທີ່ບໍ່ໄດ້ສົ່ງອອກ ຊື່ງເປັນການບັນທຶກຕົວເລືອກເທິງ struct ທີ່ບໍ່ໄດ້ສົ່ງອອກ.

```go
type options struct {
  cache  bool
  logger *zap.Logger
}

type Option interface {
  apply(*options)
}

type cacheOption bool

func (c cacheOption) apply(opts *options) {
  opts.cache = bool(c)
}

func WithCache(c bool) Option {
  return cacheOption(c)
}

type loggerOption struct {
  Log *zap.Logger
}

func (l loggerOption) apply(opts *options) {
  opts.logger = l.Log
}

func WithLogger(log *zap.Logger) Option {
  return loggerOption{Log: log}
}

// Open creates a connection.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  options := options{
    cache:  defaultCache,
    logger: zap.NewNop(),
  }

  for _, o := range opts {
    o.apply(&options)
  }

  // ...
}
```

ເບິ່ງເພີມເຕີມ

- [Self-referential functions and the design of options]
- [Functional options for friendly APIs]

  [self-referential functions and the design of options]: https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html
  [functional options for friendly apis]: https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis

# Linting

ຄວາມສຳຄັນຂອງ linters ຄື lint ຢ່າງສະໝ່ຳສະເໝີທົ່ວ codebase.

ເຮົາຂໍແນະນຳໃຫ້ໃຊ້ linters ຕໍ່ໄປນີ້ເປັນຢ່າງໜ້ອຍ ເນື່ອງຈາກມັນຊ່ວຍໃນການກວດຈັບບັນຫາທີ່ພົບຫຼາຍທີ່ສຸດ ແລະຍັງເຮັດໃຫ້ໂຄ໋ດມີຄຸນນະພາບທີ່ສູງ.

- [errcheck] ເພື່ອແນ່ໃຈວ່າ error ໄດ້ຖືກຈັດກາກຢ່າງຖືກຕ້ອງ.
- [goimports] ເພື່ອ format ໂຄ໋ດ ແລະຈັດການການ imports.
- [golint] ເພື່ອຊີ້ໃຫ້ເຫັນຂໍ້ຜິດພາດທົ່ວໄປ.
- [govet] ເພື່ອວິເຄາະໂຄ໋ດທີ່ຜິດພາດທົ່າໄປ.
- [staticcheck] ເພື່ອທຳການກວດສອບການວິເຄາະແບບຄົງທີ່ຕ່າງໆ.

  [errcheck]: https://github.com/kisielk/errcheck
  [goimports]: https://godoc.org/golang.org/x/tools/cmd/goimports
  [golint]: https://github.com/golang/lint
  [govet]: https://golang.org/cmd/vet/
  [staticcheck]: https://staticcheck.io/

## Lint Runners

ເຮົາແນະນຳໃຫ້ໃຊ້ [golangci-lint] ເປັນຕົວ go-to lint ສຳລັບໂຄ໋ດ Go. ສ່ວນໃຫຍ່ເນື່ອງຈາກປະສິດທິພາບທີ່ດີໃນສຳລັບໂຄ໋ດຂະໜາດໃຫຍ່ ແລະຄວາມສາມາດໃນການຕັ້ງຄ່າ ແລະໃຊ້ linters ມາດຕະຖານຈຳນວນຫຼາຍພ້ອມກັນ ຕົວຢ່າງ [.golangci.yml].

golangci-lint ມີ [various linters] ສຳລັບໃຊ້ງານທີ່ຫຼາກຫຼາຍ. linters ຂ້າງຕົ້ນແນະນຳໃຫ້ໃຊ້ເປັນຄ່າເລີ່ມຕົ້ນ ແລະເຮົາຂໍແນະນຳໃຫ້ທີມງານເພີ່ມ linters ເພີ່ມເຕີມຕາມຂອງເໝາະສົມຂອງໂຄງການ.

[golangci-lint]: https://github.com/golangci/golangci-lint
[.golangci.yml]: https://github.com/uber-go/guide/blob/master/.golangci.yml
[various linters]: https://golangci-lint.run/usage/linters/
