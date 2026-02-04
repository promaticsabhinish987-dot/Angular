1. we does not create route for admin.  register , we create it manually in database.
2. seed.js is used to create dumy data on database.
3. for image we display dummy image with function
4. currency can be captured from environment.
5. we have multiple environments, where we define the url for image and url for data.
6. readit /stackoverflow
7. miro for lld
8. show detail of product on top and select from below list , select id on click and scrol to top, and selet a product with that id, and display at above.
9. We can create a pipe for different status

```

'PENDING' | 'CONFIRMED' | 'SHIPPED' | 'DELIVERED' | 'CANCELLED'


>>> ng generate pipe pipes/orderStatus

//order status pipe

import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'orderStatus'
})
export class OrderStatusPipe implements PipeTransform {

  transform(status: string): string {
    if (!status) return 'Unknown';

    switch (status) {
      case 'PENDING':
        return 'Pending ⏳';

      case 'CONFIRMED':
        return 'Confirmed ✅';

      case 'SHIPPED':
        return 'Shipped 🚚';

      case 'DELIVERED':
        return 'Delivered 📦';

      case 'CANCELLED':
        return 'Cancelled ❌';

      default:
        return 'Unknown';
    }
  }
}


<p>
  Order Status:
  <span>{{ order.status | orderStatus }}</span>
</p>

order.status = 'SHIPPED';
Shipped 🚚





// pipe with other attribute (text only, chip label, or short version.)

transform(status: string, type: 'text' | 'short' = 'text'): string {
  const map: any = {
    PENDING: { text: 'Pending ⏳', short: 'P' },
    CONFIRMED: { text: 'Confirmed ✅', short: 'C' },
    SHIPPED: { text: 'Shipped 🚚', short: 'S' },
    DELIVERED: { text: 'Delivered 📦', short: 'D' },
    CANCELLED: { text: 'Cancelled ❌', short: 'X' }
  };

  return map[status]?.[type] || 'Unknown';
}

<!-- Full text -->
{{ order.status | orderStatus }}

<!-- Short label -->
{{ order.status | orderStatus:'short' }}



//css class for pipe

@Pipe({ name: 'statusClass' })
export class StatusClassPipe implements PipeTransform {

  transform(status: string): string {
    switch (status) {
      case 'PENDING': return 'status-pending';
      case 'CONFIRMED': return 'status-confirmed';
      case 'SHIPPED': return 'status-shipped';
      case 'DELIVERED': return 'status-delivered';
      case 'CANCELLED': return 'status-cancelled';
      default: return '';
    }
  }
}


<span [class]="order.status | statusClass">
  {{ order.status | orderStatus }}
</span>


```






















