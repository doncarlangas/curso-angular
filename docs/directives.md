# Directivas

Existen 3 tipos de directivas en anguar:
- Directivas de componente.
- Directivas estructurales.
- Directivas de atributo.

## Directivas de componente.

Las directivas de componente permiten incluir un componente en el HTML de otro componente.

```html
<div>
    <app-calculadora></app-calculadora>
</div>
```

## Directivas estructurales

Las directivas estructurales alteran la estructura del DOM.

### ngIf

Añade o elimina un elemento del DOM basándose en una condición.

```html
<div *ngIf="hero" >{{hero.name}}</div>
```

### ngFor

Repite una plantilla por cada elemento de una lista.

```html
<ul>
  <li *ngFor="let hero of heroes">{{hero.name}}</li>
</ul>
```

Dentro del *ngFor existen 5 variables locales:
- index: number: El índice del elemento actual.
- first: boolean: True cuando el item actual es el primero.
- last: boolean: True cuando el item actual es el último.
- even: boolean: True cuando el índice del item actual es par.
- odd: boolean: True cuando el índice del item actual es impar.

Forma de uso:

```html
<div *ngFor="let hero of heroes; let i=index; let odd=odd" [class.odd]="odd">
  ({{i}}) {{hero.name}}
</div>
```

Además existe la posibilidad de "cachear" elementos del array con trackBy. 

```html
<div *ngFor="let hero of heroes; let i=index; let odd=odd; trackBy: trackById" [class.odd]="odd">
  ({{i}}) {{hero.name}}
</div>
```


### NgSwitch

Un conjunto de directivas que permiten cambiar entre vistas alternativas.

```html
<div [ngSwitch]="hero.emotion">
  <happy-hero    *ngSwitchCase="'happy'"    [hero]="hero"></happy-hero>
  <sad-hero      *ngSwitchCase="'sad'"      [hero]="hero"></sad-hero>
  <confused-hero *ngSwitchCase="'confused'" [hero]="hero"></confused-hero>
  <unknown-hero  *ngSwitchDefault           [hero]="hero"></unknown-hero>
</div>
```

CUIDADO: Solamente se puede poner una directiva por elemento.

Existe la etiqueta &lt;ng-container> que no se introduce en el DOM.

```html
<div>
  Elige a tu héroe favorito
  (<label><input type="checkbox" checked (change)="showSad = !showSad">ocultar héroes tristes</label>).
</div>
<select [(ngModel)]="hero">
  <ng-container *ngFor="let h of heroes">
    <ng-container *ngIf="showSad || h.emotion !== 'sad'">
      <option [ngValue]="h">{{h.name}} ({{h.emotion}})</option>
    </ng-container>
  </ng-container>
</select>
```

## Directivas de atributo

Las directivas de atributo se usan como si fueran atributos de los elementos HTML.

Ejemplos de directivas de atributo de angular:
- NgClass
- NgModel
- NgForm

Ejemplo: 

```html
<div [class.my-class1]="step==1" [class.my-class2]="step==2"></div>
<div [ngClass]="{'my-class1': step==1,'my-class2': step==2}"></div>
<div [ngClass]="{1:'my-class1',2:'my-class2',3:'my-class3'}[step]"></div>
<div [ngClass]="(step==1)?'my-class1':'my-class2'"></div>
```

Iremos viendo muchas directivas de atributo durante el curso.

### Directivas de atributo personalizadas

Una directiva de atributo es una clase decorada con el decorador *@Directive*:

```typescript
import { Directive, ElementRef } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(el: ElementRef) { }
}
```

No hay que olvidarse de declarar la directiva en el *declarations* del módulo.

En vez de crearla a mano, podemos generarla con el CLI de Angular:

> ng generate directive highlight

Al constructor le llega un objeto *ElementRef* que es el elemento sobre el que se está aplicando la directiva.

Cuando angular encuentre *appHighlight* en un HTML, aplicará la directiva.

Ejemplo:

```html
<p appHighlight>Highlight me!</p>
```

```typescript
import { Directive, ElementRef } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(el: ElementRef) {
    el.nativeElement.style.backgroundColor = 'yellow';
  }
}
```

Esto hace que el elemento sobre el que se aplica la directiva tenga color de fondo amarillo.

#### El decorador @HostListener

El decorador @HostListener permite que la directiva responda a eventos que ocurran en el elemento:

```typescript
import { Directive, ElementRef, HostListener } from '@angular/core';
 
@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(private el: ElementRef) { }
 
  @HostListener('mouseenter') onMouseEnter() {
    this.highlight('yellow');
  }
 
  @HostListener('mouseleave') onMouseLeave() {
    this.highlight(null);
  }
 
  private highlight(color: string) {
    this.el.nativeElement.style.backgroundColor = color;
  }
}
```

#### El decorador @Input

El decorador @Input permite pasar variables a la directiva.

Hagamos un ejemplo en el que al aplicar la directiva *appHighlight* a un elemento, tengamos la posibilidad de elegir el color

```html
<p appHighlight [highlightColor]="color">Texto del párrafo</p>
```

```typescript
import { Directive, ElementRef, HostListener, Input } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {

  constructor(private el: ElementRef) { }

  @Input() highlightColor: string;

  @HostListener('mouseenter') onMouseEnter() {
    this.highlight(this.highlightColor || 'red');
  }

  @HostListener('mouseleave') onMouseLeave() {
    this.highlight(null);
  }

  private highlight(color: string) {
    this.el.nativeElement.style.backgroundColor = color;
  }
}
```

Se puede abreviar la directiva si se utiliza el alias de @Input.

```html
<p [appHighlight]="color">Texto del párrafo</p>
```

```typescript
import { Directive, ElementRef, HostListener, Input } from '@angular/core';

@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {

  constructor(private el: ElementRef) { }

  @Input('appHighlight') highlightColor: string;

  @HostListener('mouseenter') onMouseEnter() {
    this.highlight(this.highlightColor || 'red');
  }

  @HostListener('mouseleave') onMouseLeave() {
    this.highlight(null);
  }

  private highlight(color: string) {
    this.el.nativeElement.style.backgroundColor = color;
  }
}
```







[Índice](index.md)
