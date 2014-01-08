Ip Filter
=========

[![Build Status](https://travis-ci.org/Spomky/SpomkyIpFilterBundle.png?branch=master)](https://travis-ci.org/Spomky/SpomkyIpFilterBundle)
[![Scrutinizer Quality Score](https://scrutinizer-ci.com/g/Spomky/SpomkyIpFilterBundle/badges/quality-score.png?s=0efa430fe0181ddc440501ff995c7e94cf15aa28)](https://scrutinizer-ci.com/g/Spomky/SpomkyIpFilterBundle/)
[![Code Coverage](https://scrutinizer-ci.com/g/Spomky/SpomkyIpFilterBundle/badges/coverage.png?s=a942daa4e0ecb71504a3e8b8c0e80e2ba144b308)](https://scrutinizer-ci.com/g/Spomky/SpomkyIpFilterBundle/)

[![SensioLabsInsight](https://insight.sensiolabs.com/projects/1b7fe376-73e5-41aa-9646-8be3ad514a7d/big.png)](https://insight.sensiolabs.com/projects/1b7fe376-73e5-41aa-9646-8be3ad514a7d)

[![Dependency Status](https://www.versioneye.com/user/projects/52caa8d4ec137551e4000226/badge.png)](https://www.versioneye.com/user/projects/52caa8d4ec137551e4000226)

This bundle will help you to restrict access of your application using `IP addresses` and `ranges of IP addresses`.

It supports both `IPv4` and `IPv6` addresses and multiple environments.

For example, you can grant access of a range of addresses from `192.168.1.1` to `192.168.1.100` on `dev` and `test` environments and deny all others.

# Prerequisites #

This version of the bundle requires `Symfony 2.4`.
It only supports `Doctrine ORM`.

# Policy #

Please note that authorized IPs have a higher priority than unauthorized ones.
For example, if range `192.168.1.10` to `192.168.1.100` is **unauthorized** and `192.168.1.20` is **authorized**, `192.168.1.20` will be granted. 

# Installation #

Installation is a quick 4 steps process:

* Download `SpomkyIpFilterBundle`
* Enable the Bundle
* Create your model class
* Configure the `SpomkyIpFilterBundle`

##Step 1: Install SpomkyIpFilterBundle##

The preferred way to install this bundle is to rely on Composer. Just check on Packagist the version you want to install (in the following example, we used "dev-master") and add it to your `composer.json`:

	{
	    "require": {
	        // ...
	        "spomky/ip-filter-bundle": "dev-master"
	    }
	}

##Step 2: Enable the bundle##

Finally, enable the bundle in the kernel:

	<?php
	// app/AppKernel.php
	
	public function registerBundles()
	{
	    $bundles = array(
	        // ...
	        new Spomky\IpFilterBundle\SpomkyIpFilterBundle(),
	    );
	}

##Step 3: Create IP and Range classes##

This bundle needs to persist filtered IPs and ranges to a database:

Your first job, then, is to create these classes for your application.
These classes can look and act however you want: add any properties or methods you find useful.

In the following sections, you'll see an example of how your classes should look.

Your classe can live inside any bundle in your application.
For example, if you work at "Acme" company, then you might create a bundle called `AcmeIpBundle` and place your classes in it.

Ip Repository and Range Repository classes are important. You can use those provided with this bundle or extend them to include your own classes.

The IP field type must be `ipaddress`.

###Ip class:###

	<?php
	// src/Acme/IpBundle/Entity/Ip.php
	
	namespace Acme\IpBundle\Entity;
	
	use Spomky\IpFilterBundle\Model\Ip as BaseIp;
	use Doctrine\ORM\Mapping as ORM;
	
	/**
	 * Ip
	 *
	 * @ORM\Table(name="ips")
	 * @ORM\Entity(repositoryClass="Spomky\IpFilterBundle\Model\IpRepository")
	 */
	class Ip extends BaseIp
	{
	    /**
	     * @var integer $id
	     *
	     * @ORM\Column(name="id", type="integer")
	     * @ORM\Id
	     * @ORM\GeneratedValue(strategy="AUTO")
	     */
	    protected $id;
	
	    /**
	     * @var ipaddress $ip
	     *
	     * @ORM\Column(name="ip", type="ipaddress")
	     */
	    protected $ip;
	
	    /**
	     * @var string $environment
	     *
	     * @ORM\Column(name="environment", type="string", length=10, nullable=true)
	     */
	    protected $environment;
	
	    /**
	     * @var boolean $authorized
	     *
	     * @ORM\Column(name="authorized", type="boolean")
	     */
	    protected $authorized;
	
	    public function getId() {
	        return $this->id;
	    }
	
	    public function setIp($ip) {
	        $this->ip = $ip;
	        return $this;
	    }
	
	    public function setEnvironment($environment) {
	        $this->environment = $environment;
	        return $this;
	    }
	
	    public function setAuthorized($authorized) {
	        $this->authorized = $authorized;
	        return $this;
	    }
	}

-

	<?php
	// src/Acme/IpBundle/Entity/Range.php
	
	namespace Acme\IpBundle\Entity;
	
	use Spomky\IpFilterBundle\Model\Range as BaseRange;
	use Doctrine\ORM\Mapping as ORM;
	
	/**
	 * Range
	 *
	 * @ORM\Table(name="ranges")
	 * @ORM\Entity(repositoryClass="Spomky\IpFilterBundle\Model\RangeRepository")
	 */
	class Range extends BaseRange
	{
	    /**
	     * @var integer $id
	     *
	     * @ORM\Column(name="id", type="integer")
	     * @ORM\Id
	     * @ORM\GeneratedValue(strategy="AUTO")
	     */
	    protected $id;
	
	    /**
	     * @var ipaddress $start_ip
	     *
	     * @ORM\Column(name="start_ip", type="ipaddress")
	     */
	    protected $start_ip;
	
	    /**
	     * @var ipaddress $end_ip
	     *
	     * @ORM\Column(name="end_ip", type="ipaddress")
	     */
	    protected $end_ip;
	
	    /**
	     * @var string $environment
	     *
	     * @ORM\Column(name="environment", type="string", length=10, nullable=true)
	     */
	    protected $environment;
	
	    /**
	     * @var boolean $authorized
	     *
	     * @ORM\Column(name="authorized", type="boolean")
	     */
	    protected $authorized;
	
	    public function getId() {
	        return $this->id;
	    }
	
	    public function setStartIp($start_ip) {
	        $this->start_ip = $start_ip;
	        return $this;
	    }
	
	    public function setEndIp($end_ip) {
	        $this->end_ip = $end_ip;
	        return $this;
	    }
	
	    public function setEnvironment($environment) {
	        $this->environment = $environment;
	        return $this;
	    }
	
	    public function setAuthorized($authorized) {
	        $this->authorized = $authorized;
	        return $this;
	    }
	}

##Step 4: Configure your application##

### Set your classes and managers ###

	# app/config/config.yml
	spomky_ip_filter:
	    db_driver: orm        # Driver available: orm
	    ip_class:             Acme\IpBundle\Entity\Ip
	    range_class:          Acme\IpBundle\Entity\Range

If you have your own managers, you can use them. They just need to implement `Spomky\IpFilterBundle\Model\IpManagerInterface` or `Spomky\IpFilterBundle\Model\RangeManagerInterface`.

	# app/config/config.yml
	spomky_ip_filter:
	    ...
	    ip_manager: my.custom.ip.manager
	    range_manager: my.custom.range.manager

###Security Strategy###

In order for this bundle to take effect, you need to change the default access decision strategy, which, by default, grants access if any voter grants access.

You also need to place your site behind a firewall rule.

    # app/config/security.yml
    security:
        access_decision_manager:
            strategy: unanimous
	…
    firewalls: 
        my_site:
            pattern: ^/
            anonymous: ~

    access_control:
        - { path: ^/, roles: IS_AUTHENTICATED_ANONYMOUSLY }

# How to #

## Small example ##

How to grant access for `192.168.1.10` on `dev` and `test` environments and deny all others?


	<?php
	// src/Acme/IpBundle/Controller/IpController.php
	
	namespace Spomky\MyRolesBundle\Controller;
	
	use Symfony\Bundle\FrameworkBundle\Controller\Controller;
	
	use Acme\IpBundle\Entity\Ip;
	use Acme\IpBundle\Entity\Range;
	
	class IpController extends Controller
	{
	    public function addAction()
	    {
			//Create your IP
	        $ip = new Ip;
	        $ip->setIp('192.168.1.10');
	        $ip->setEnvironment('dev,test');
	        $ip->setAuthorized(true);

			//Create your range
	        $range = new Range;
	        $range->setStartIp('0.0.0.1');
	        $range->setEndIp('255.255.254');
	        $range->setEnvironment('dev,test');
	        $range->setAuthorized(false);
	
			//Get Doctrine entity manager
	        $em = $this->getDoctrine()->getManager();

			//Persist entities
	        $em->persist($range);
	        $em->persist($ip);

			//And flush
	        $em->flush();
	    }
	}

## Network support ##

Network can be supported using a Range object. You just need to get first and last IP addresses.
This bundle provides a range calculator, so you can easily extend your range entity using it.

	<?php
	// src/Acme/IpBundle/Entity/Range.php
	
	namespace Acme\IpBundle\Entity;
	
	use Spomky\IpFilterBundle\Model\Range as BaseRange;
	use Doctrine\ORM\Mapping as ORM;

	use Spomky\IpFilterBundle\Tool\Network;
	
	…
		public function setNetwork($network) {

			$range = Network::getRange($network);
			$this->setStartIp($range['start']);
			$this->setEndIp($range['end']);
        }
	…

Now, you can allow or deny a whole network. In the following example, we will deny access of all IP addresses except our local network.

	//All IP addresses
	$all = new Range;
	$all->setNetwork('0.0.0.0/0');
	$all->setEnvironment('dev,test');
	$all->setAuthorized(false);

	/My local network (IPv4)
	$local = new Range;
	$local->setNetwork('192.168.0.0/16');
	$local->setEnvironment('dev,test');
	$local->setAuthorized(true);

	/Another local network (IPv6)
	$local_6 = new Range;
	$local_6->setNetwork('fe80::/64');
	$local_6->setEnvironment('dev,test');
	$local_6->setAuthorized(true);
	
	//Get Doctrine entity manager
	$em = $this->getDoctrine()->getManager();
	
	//Persist entities
	$em->persist($all);
	$em->persist($local);
	$em->persist($local_6);
	
	//And flush
	$em->flush();

## app_dev.php ##

In the Symfony2 Standard Edition, `app_dev.php` restrict access of the `dev` environment:

	…
	if (isset($_SERVER['HTTP_CLIENT_IP'])
	    || isset($_SERVER['HTTP_X_FORWARDED_FOR'])
	    || !in_array(@$_SERVER['REMOTE_ADDR'], array('127.0.0.1', 'fe80::1', '::1'))
	) {
	    header('HTTP/1.0 403 Forbidden');
	    exit('You are not allowed to access this file. Check '.basename(__FILE__).' for more information.');
	}
…

You can do exactly the same using this bundle:

	//All IP addresses are denied
	$all = new Range;
	$all->setNetwork('0.0.0.0/0');
	$all->setEnvironment('dev');
	$all->setAuthorized(false);

	$all6 = new Range;
	$all6->setNetwork('::/0');
	$all6->setEnvironment('dev');
	$all6->setAuthorized(false);

	//127.0.0.1 (IPv4 localhost)
    $localhost = new Ip;
    $localhost->setIp('127.0.0.1');
    $localhost->setEnvironment('dev');
    $localhost->setAuthorized(true);

	//::1 (IPv6 localhost)
    $localhost6 = new Ip;
    $localhost6->setIp('::1');
    $localhost6->setEnvironment('dev');
    $localhost6->setAuthorized(true);

	//fe80::1 (IPv6 local link)
    $locallink6 = new Ip;
    $locallink6->setIp('fe80::1');
    $locallink6->setEnvironment('dev');
    $locallink6->setAuthorized(true);
	
	//Get Doctrine entity manager
	$em = $this->getDoctrine()->getManager();
	
	//Persist entities
	$em->persist($all);
	$em->persist($all6);
	$em->persist($localhost);
	$em->persist($localhost6);
	$em->persist($locallink6);

	//And flush
	$em->flush();