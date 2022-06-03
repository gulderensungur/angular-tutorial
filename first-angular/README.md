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
