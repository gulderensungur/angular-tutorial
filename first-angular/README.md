# Angular Notları

### How to install Angular?

npm install -g @angular/cli

### How to create new project?

ng new project-name

### How to Serve Application?

ng serve --open

### How to create new component?

ng generate component componnets/foldername

## Angular Files/Folders Structure

Angular projesini oluşturduktan sonra karşımıza karmaşık görünen bir dosya dizini çıkar. Göründüğü kadar karmaşık olmamakla birlikte, başlangıç aşamazında ilgileneceğiz alan src kısmı. src kısmında neler var ve ne işe yarıyor bakalım.

Burdaki app.component’li dosyalarımız projenin parent dosyasını ifade ediyor. Oluşturduğumuz componentleri app.componet.html dosyasında çağırıyoruz. React’ten farklı olarak her bir component için farklı farklı .html .ts .css ve test dosyaları oluşturuluyor. Component’in bir başka parent component içinde çağırılması şu şekilde oluyor:

```tsx
<div class="container">
  <app-header></app-header>
</div>
```

Yukarıdaki kod bloğu app.componet.html dosyasında bulunmaktadır. header componenti app componnetin içinde çağırılmıştır.

Eğer global bir css yazmak istersek styles.css ‘i kullanıyoruz.

## Properties:

React’ten sonra en faklı gelen yapılardan biri properties yapısı oldu. Her şeyden önce en beğendiğim konu parent-child child-parent arasında iletişimin oldukça kolay olması. Bunun için Input ve Output fonksiyonlarını kullanıyoruz. Örnek verelim:

```tsx
<header>
  <h1>{{ title }}</h1>
  <app-button
    color="green"
    text="Add"
    (btnClick)="toggleAddTask()"
  ></app-button>
</header>
```

Yukarıdaki kodaa header componentinde buton componentini çağırdık. Ancak buton componenti farklı yerlerde kullanılacağı için farklı özelliklerde ve farklı fonksiyonları olabilir. Üstteki kodda butona color, text ve btnClick() propu gönderdik.

```tsx
import { Component, OnInit, Input, Output, EventEmitter } from "@angular/core";

@Component({
  selector: "app-button",
  templateUrl: "./button.component.html",
  styleUrls: ["./button.component.css"],
})
export class ButtonComponent implements OnInit {
  @Input() text!: string;
  @Input() color!: string;

  @Output() btnClick = new EventEmitter();

  onClick = () => {
    this.btnClick.emit();
  };

  constructor() {}

  ngOnInit(): void {}
}
```

header kısmında gönderdiğimiz propu Input ile aldık ve type’ını aldık. Fakat btnClick fonksiyonumuzu burda Output ile aldık. Bunun sebebi bu fonksiyonun child’dan parent’a gönderilmesidir. click gibi evet yazarken emit yapısını kullanırız.

Ve bunu button Componentimizin html’inde şu şekilde kullanırız:

```tsx
<button
  [ngStyle]="{ 'background-color': color }"
  class="btn"
  (click)="onClick()"
>
  {{ text }}
</button>
```

\*ngFor yönergesi, HTML şablonunun bir bölümünü yinelenebilir bir listedeki her öğe için bir kez tekrarlamak için kullanılır.(map() e benziyor)

## Angularda Veri Çekmek

Mock data için aşağıdaki şu yollar izlenir:

Öncelikle verilerimizi app klasörüne manuel olarak ekliyoruz ve verilerin yapısını belirtmek için interface oluşturuyoruz.

**Mock Data için:**

```tsx
import { Task } from './Task';

export const TASKS: Task[] = [
  {
    id: 1,
    text: 'Doctors Appointment',
    day: 'May 5th at 2:30pm',
    reminder: true,
  },
```

**Interface için:**

```tsx
export interface Task {
  id?: number;
  text: string;
  day: string;
  reminder: boolean;
}
```

Bu aşamada verilerimizi oluşturduk ve onları çekmemiz gerekiyor. Açıkçası benim kafamı karıştıran noktalardan biri bu oldu. Aşağıda anlaşılır bir şekilde anlatmaya çalışacağım.

```tsx
import { Injectable } from "@angular/core";
import { Task } from "../Task";
import { TASKS } from "../mock-tasks";

@Injectable({
  providedIn: "root",
})
export class TaskService {
  constructor() {}
  getTasks(): Task[] {
    return TASKS;
  }
}
```

Oluşturulan verileri services adında bir dosyanın içindeki task.service.ts dosyasının içinde import edip, getTasks() fonksiyonu ile return ediyoruz.

```tsx
import { Component, OnInit } from "@angular/core";
import { TaskService } from "../../services/task.service";
import { Task } from "../../Task";

@Component({
  selector: "app-tasks",
  templateUrl: "./tasks.component.html",
  styleUrls: ["./tasks.component.css"],
})
export class TasksComponent implements OnInit {
  tasks: Task[] = []; //datadaki tüm veriler için

  constructor(private taskService: TaskService) {}

  ngOnInit(): void {
    this.tasks = this.taskService.getTasks();
  }
}
```

task.servicede oluşturduğumuz fonksiyonu kullanarak bunu TasksComponnet’inin içinde verileri çekiyoruz.Buradaki önemli nokta TaskService’i constructer içinde tanımlamak. Daha sonra her bir task verisi için React’ten bildiğimiz map() yapısına benzer \*ngFor yapısını kullanıyoruz.

```tsx
<app-task-item *ngFor="let task of tasks" [task]="task"></app-task-item>
```

Bundan sonraki aşama, her bir task verisi için oluşturduğumuz task-item’a verileri kullanmak.

```tsx
import { Component, OnInit, Input } from "@angular/core";
import { Task } from "../../Task";

@Component({
  selector: "app-task-item",
  templateUrl: "./task-item.component.html",
  styleUrls: ["./task-item.component.css"],
})
export class TaskItemComponent implements OnInit {
  @Input() task!: Task;
  constructor() {}

  ngOnInit(): void {}
}
```

Artık verileri task-item.html dosyasında kullanabiliriz.

```tsx
<div class="task">
  <h3>
    {{ task.text }}
  </h3>
  <p>{{ task.day }}</p>
</div>
```

## Day 3

In this project, I want to add some styles if the reminder in task data is true. So, we go to _task-item.ts_ file and add _[ngClass] = “{reminder: tasks.reminder}”_ to div class that named task. Also, I want to change the reminder’s value when I click the task-item’s div. I can change the value like the way that I did in the previous deleteTask().

Firstly I defined a function in task-item html file.

```tsx
<div
  [ngClass]="{ reminder: task.reminder }"
  class="task"
  (dblclick)="onToggle(task)"
>
  <h3>
    {{ task.text }}
    <fa-icon
      [ngStyle]="{ color: 'red' }"
      [icon]="faTimes"
      (click)="onDelete(task)"
    ></fa-icon>
  </h3>
  <p>{{ task.day }}</p>
</div>
```

In this code we can see dblclick function. Then we define this funtion in task-item.ts file.

```tsx
@Output() onToggleReminder: EventEmitter<Task> = new EventEmitter();

onToggle(task: Task) {
    this.onToggleReminder.emit(task);
  }
```

We add codes on above and we will define function that describe for these function to parent component.(tasks component) We add _(onToggleReminder)="toggleReminder(task)”_ in tasks.component.html file.

```tsx
toggleReminder(task: Task) {
    task.reminder = !task.reminder;
    this.taskService.updateTaskReminder(task).subscribe();
  }
```

And we see that undefined funtion named updateTaskReminder(). This function needs to be define in task.service because when we dont update in service our operation will lost when page is re-render.

```tsx
const httpOptions = {
  headers: new HttpHeaders({
    'Content-Type': 'application/json',
  }),
};

updateTaskReminder(task: Task): Observable<Task> {
    const url = `${this.apiUrl}/${task.id}`;
    return this.http.put<Task>(url, task, httpOptions);
  }
}
```

On the above code, note that, put method take 3 different parametres. httpOption includes our additional information. But if we are update with put() method to something, we have to define httpOption. (see below quote)

> HttpHeaders let the client and server share additional information http requests and response

### Add Task:

We created a add-task component. And we add form div in add-task.html. There is an important thing here. let me check codes.

```tsx
<form class="add-form" (ngSubmit)="onSubmit()">
  <div class="form-control">
    <label for="text">Add Task</label>
    <input
      type="text"
      name="text"
      [(ngModel)]="text"
      id="text"
      placeholder="Add Task"
    />
  </div>
  <div class="form-control">
    <label for="text">Day & Time</label>
    <input
      type="text"
      name="day"
      [(ngModel)]="day"
      id="day"
      placeholder="Add Day & Time"
    />
  </div>
  <div class="form-control form-control-check">
    <label for="reminder">Reminder</label>
    <input
      type="checkbox"
      name="reminder"
      [(ngModel)]="reminder"
      id="reminder"
    />
  </div>

  <input type="submit" value="Save Text" class="btn btn-block" />
</form>
```

We see that [(ngModel)] = “value” this is for two way binding. And also we see that ngSubmit function for submit to form. We saw the previous part that how to add method/function to both of parent and child.

**Two way data binding:**

İki yönlü veri bağlama, veri giriş formlarında kullanışlıdır. Bir kullanıcı bir form alanında değişiklik yaptığında, modelimizi güncellemek isteriz. Benzer şekilde, modeli yeni verilerle güncellediğimizde görünümü de güncellemek istiyoruz.

**ngModel:**

The Angular uses the `ngModel` directive to achieve the two-way binding on HTML Form elements. It binds to a form element like `input`, `select`, `selectarea`. etc.

The `ngModel`directive is not part of the Angular Core library. It is part of the `FormsModule`library. You need to import the `FormsModule`package into your Angular module.

### **Understand difference between constructor & ngOnInit()**

Starting with the basics, the main role of ngOnInit is to provide a signal that Angular has done initializing the component and that users can roll on further. The constructor, on the other hand, is significantly used to initialize the class members but it is unable to perform the whole work. It is only beneficial in the case of dependency injection and initialization of the class field. That being said, the compiler should actually avoid writing the work on Constructor. ngOnInit is a better place to write work code that is required at the time of class instantiation.

### Toogle Button & UI Service

If an operation affects on more than one components, we use UI service. For example, in our task project, add-task component should be invisible. When we click Add button, add-task component will be visible and button’s color and text will be changed. So when we click the add button we have one more than the destination component. When we create ui.service file we need to import Observable and Subject. The way to communicate between components is to use an `Observable`
 and a `Subject` (which is a type of observable)

**Subject:**

A Subject is like an Observable, but can multicast to many Observers. Subjects are like EventEmitters: they maintain a registry of many listeners.(See [here](https://rxjs.dev/guide/subject))

```tsx
import { Injectable } from "@angular/core";
import { faTachographDigital } from "@fortawesome/free-solid-svg-icons";
import { Observable, Subject } from "rxjs";

@Injectable({
  providedIn: "root",
})
export class UiService {
  private showAddTask: boolean = false;
  private subject = new Subject<any>();

  constructor() {}

  toggleAddTask(): void {
    this.showAddTask = !this.showAddTask;
    //if we want to clear all thing we use "this.subject.next()";
    this.subject.next(this.showAddTask);
  }

  onToggle(): Observable<any> {
    return this.subject.asObservable();
  }
}
```

**Subject**.**next();**

The subject next method is used to send messages to an observable which are then sent to all angular components that are subscribers of that observable.

After we create ui.service we organized the affected component when click the add button. So we will go to header and add-task component.

**header.component.ts:**

```tsx
import { Component, OnInit } from "@angular/core";
import { UiService } from "../../services/ui.service";
import { Subscription } from "rxjs";
@Component({
  selector: "app-header",
  templateUrl: "./header.component.html",
  styleUrls: ["./header.component.css"],
})
export class HeaderComponent implements OnInit {
  title: string = "Task Tracker";
  showAddTask!: boolean;
  subscription!: Subscription;

  constructor(private uiService: UiService) {
    this.subscription = this.uiService
      .onToggle()
      .subscribe((value) => (this.showAddTask = value));
  }

  ngOnInit(): void {}

  toggleAddTask() {
    this.uiService.toggleAddTask();
  }
}
```

In here we import Subscription that we haven’t talked about before. (Subscription, bir Observable’ın yürütülmesi gibi tek kullanımlık bir kaynağı temsil eder.) There is an important thing that I mention about it. You can see the constructor hook on above code. When I code ui.service.ts file, I defined default value of _showAddTask_’s as a false and then I change the value in _toggleAddTask()_ function.

But when I go to my toogleAddTask function in header.component.ts file and I call the same function from uiService, I reliaze that showAddTask value didn’t update althought indicate in the toggleAddTask in uiService. This situation caused that there is no change when I click the add button. Thats why we add subscription and th other codes in constructor hook.

Also, I need to apply all the subscription operation in the add-task component.

```tsx
import { Component, OnInit, Output, EventEmitter } from "@angular/core";
import { UiService } from "src/app/services/ui.service";
import { Subscription } from "rxjs";

@Component({
  selector: "app-add-task",
  templateUrl: "./add-task.component.html",
  styleUrls: ["./add-task.component.css"],
})
export class AddTaskComponent implements OnInit {
  @Output() onAddTask = new EventEmitter();

  text!: string;
  day!: string;
  reminder: boolean = false;
  showAddTask!: boolean;
  subscription = new Subscription();

  constructor(private uiService: UiService) {
    this.subscription = this.uiService
      .onToggle()
      .subscribe((value) => (this.showAddTask = value));
  }

  ngOnInit(): void {}

  onSubmit() {
    if (!this.text) {
      alert("Task field can not be empty!");
      return;
    }

    const newTask = {
      text: this.text,
      day: this.day,
      reminder: this.reminder,
    };

    this.onAddTask.emit(newTask);

    this.text = "";
    this.day = "";
    this.reminder = false;
  }
}
```

And the last step is, change the visiblity of add-task form in ui. So, we add \*_ngIf="showAddTask” code in add-task.component in html. So now, if showAddTask is false. my add-task form doesnt seen in ui._

**Angular Router:**

If we if we didn’t select the option to add a router when creating the project, we need to add to app.module.

```tsx
import { RouterModule, Routes } from "@angular/router";

const appRoutes: Routes = [
  { path: "", component: TasksComponent },
  { path: "about", component: AboutComponent },
];

//we add this code to imports in NgModule
//enable tracing is for debug for router
RouterModule.forRoot(appRoutes, { enableTracing: true });
```

After add Router to app.module we create about component and add these to html file.

```tsx
<div>
  <h2>About</h2>
  <h4>Version: 1.0.0</h4>
  <a routerLink="/">Go back</a>
</div>
```

About components path already defined in app.module.
