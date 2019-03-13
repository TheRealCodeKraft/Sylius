How to remove a predefined promotion rule?
==========================================

Promotion rule such as "Shipping country" is useless if your website is available only in on country. You can remove it in a compiler pass.

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
        $parameter = $container->getParameter('sylius.promotion_rules');

        $rulesToRemove = [
            'sylius.promotion_rule_checker.shipping_country',
        ];

        foreach ($rulesToRemove as $ruleToRemove) {
            $definition = $container->getDefinition($ruleToRemove);
            $type = $definition->getClass()::TYPE;
            unset($parameter[$type]);
        }

        $container->setParameter('sylius.promotion_rules', $parameter);
    }
}
```
