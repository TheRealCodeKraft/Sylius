How to remove a predefined promotion action?
============================================

Some promotion can be not appropriate to some business. Here is method to remove promotion actions in a compiler pass.

Add a compiler pass into your project
-------------------------------------

In your kernel project class, you can add a custom compiler pass.

```php
namespace App;

use App\DependencyInjection\Compiler\PromotionPass;

final class Kernel extends BaseKernel
{
    // ...

    protected function build(ContainerBuilder $container): void
    {
        $container->addCompilerPass(new PromotionPass());
    }
}
```

Remove project rule
-------------------

```php
namespace App\DependencyInjection\Compiler;

use Symfony\Component\DependencyInjection\Compiler\CompilerPassInterface;
use Symfony\Component\DependencyInjection\ContainerBuilder;

class PromotionPass implements CompilerPassInterface
{
    public function process(ContainerBuilder $container): void
    {
        $parameter = $container->getParameter('sylius.promotion_actions');

        $actionsToRemove = [
            'sylius.promotion_action.shipping_percentage_discount'
        ];

        foreach ($actionsToRemove as $actionToRemove) {
            $definition = $container->getDefinition($actionToRemove);
            $type = $definition->getClass()::TYPE;
            unset($parameter[$type]);
        }

        $container->setParameter('sylius.promotion_actions', $parameter);
    }
}
```
