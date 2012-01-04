#
# $Name: release2_0-16 $
# $Id: Makefile,v 1.52 2002/05/30 14:13:07 ttsai Exp $
#
# Copyright (C) 2002 Avaya Labs, Avaya Inc.
# Copyright (C) 1999 Bell Labs, Lucent Technologies.
# Copyright (C) Arash Baratloo, Timothy Tsai, and Navjot Singh.
# 
# This file is based on the Libsafe library, version 2.x.
# 
# This library is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.
# 
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.
# 
# You should have received a copy of the GNU Lesser General Public
# License along with this library; if not, write to the Free Software
# Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA


MAJOR_VERSION	= 2
MINOR_VERSION	= 0
PATCHLEVEL	= 16

VERSION		= $(MAJOR_VERSION).$(MINOR_VERSION).$(PATCHLEVEL)

LIBNAME		= libDefender.so.$(VERSION)
LIBLINK		= libDefender.so.$(MAJOR_VERSION)
LIBDIR		= /lib
PRELOAD		= /etc/ld.so.preload

MANNAME		= ../doc/libDefender.8
MANDIR		= /usr/man/man8

CC		= gcc
CPP		= g++
DEPEND		= gcc -M
RANLIB		= ranlib
INSTALL		= install

LDFLAGS		= -shared -Wl,-soname,${LIBLINK} -ldl $(LIBPRELUDE_LIBS) -lunwind
SRCS		= util.c intercept.c
OBJECTS		= util.o intercept.o
HEADERS		= log.h util.h

###############################################################################

# DEBUGGING OPTIONS
#
# Use
# 	-DDEBUG_TURN_OFF_SYSLOG to temporarily turn off logging violations to
#	 	syslog.  ONLY USE THIS FOR DEBUGGING!
#	-DDUMP_STACK to see a printout of the stack contents when a violation
#		is detected (dumps to LIBSAFE_DUMP_STACK_FILE or stderr if that
#		file can't be created)
#	-DDUMP_CORE to create a core dump when terminating the program.
DEBUG_FLAGS	= -DDEBUG_TURN_OFF_SYSLOG \
		  -DDUMP_STACK \
		  -DLIBDEFENDER_DUMP_STACK_FILE=\"/tmp/libdefender_stack_dump\" \
		  -DDUMP_CORE \
CCFLAGS		= -O2 -Wall -fPIC -DLIBSAFE_VERSION=\"$(VERSION)\"
# Uncomment the following line to enable all debugging options.  Better way is
# to do "make debug".
#CCFLAGS		= -O2 -Wall -fPIC $(DEBUG_FLAGS)

# For Red Hat 5.2 and other distributions that don't support wcsnlen(),
# uncomment the following:
#CCFLAGS		+= -DMISSING_WCSNLEN

###############################################################################


all	::	$(LIBNAME)

%.o	:	%.c
		$(CC) -c -o $@ $(CCFLAGS) $<

${LIBNAME}:	dep $(OBJECTS)
		$(CC) -o ${LIBNAME} $(LDFLAGS) ${OBJECTS}
		ln -sf $(LIBNAME) libDefender.so

debug	:	CCFLAGS += $(DEBUG_FLAGS)
debug	:	all

dep	::	$(SRCS) $(HEADERS)
		$(DEPEND) $(CFLAGS) $(SRCS) > dep

clean	::
		rm -f core *.bak *% *~ *.o a.out dep
		rm -f libDefender.so*

purge	::	clean
		rm -f $(LIBNAME)

install	::	all


		@if [ -f $(PRELOAD) ] ; then \
		 echo "Removing libDefender from $(PRELOAD) (if exists)"; \
		 cp -fp $(PRELOAD) $(PRELOAD).save ; \
		 grep -v libDefender $(PRELOAD) > $(PRELOAD).tmp ; \
		 rm -f $(PRELOAD) ;\
		fi; \

		$(INSTALL) $(LIBNAME) $(LIBDIR) 
		/sbin/ldconfig

		 @if [ -s $(PRELOAD).tmp ] ; then \
		  cp -fp $(PRELOAD).tmp $(PRELOAD);\
		 fi ;\
		 rm -f $(PRELOAD).tmp 

		@echo -n "Type y for installing libDefender system wide?[default n] ";\
		read reply; \
		if [ "$$reply" = 'y' ] ; then \
	         if [ -f $(LIBDIR)/$(LIBLINK) ] ; then \
		  echo "$(LIBDIR)/$(LIBLINK)" >> $(PRELOAD) ; \
		 fi; \
		fi

# If you have man pages in some other directory  add it here.

		@if [  -d $(MANDIR) ];  then \
		 echo "$(INSTALL) $(MANNAME) $(MANDIR)"; \
		 $(INSTALL) $(MANNAME) $(MANDIR); \
		elif [  -d /usr/share/man/man8 ];  then \
		  echo "$(INSTALL) $(MANNAME) /usr/share/man/man8"; \
		  $(INSTALL) $(MANNAME) /usr/share/man/man8; \
		else echo "***** Error: Cannot find man directories"; \
		fi

